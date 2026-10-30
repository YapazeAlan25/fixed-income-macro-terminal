# Datos y pipeline

## Qué es

Un sistema de dos capas construido para reemplazar el trabajo manual de armar y actualizar planillas de bonos y macro todos los días:

1. **Capa de datos (ETL):** ingesta automatizada desde BCRA, INDEC, FRED/ECB/BIS/Tesoro de EE.UU., datos.gob.ar, ARCA, la Secretaría de Finanzas, la API de datos del FMI y los prospectos de bonos del MAE, hacia un data warehouse analítico en DuckDB/Parquet. Cada fuente se valida antes de guardarse (estructura, historia mínima, frescura) y nunca pisa un archivo bueno con uno incompleto.
2. **Capa de análisis y presentación:** motores de pricing, valor relativo y detección de arbitraje propios para renta fija argentina —orquestados con Prefect—, un escritorio macro (Argentina y global) interactivo en Streamlit y un módulo de seguimiento de cartera.

La lógica financiera y las decisiones de arquitectura son de autoría propia; la implementación de código fue desarrollada con asistencia de agentes de IA (Claude, Gemini) bajo un enfoque de *AI-augmented development* — dirigido por criterio financiero, no por experiencia previa como programador.

## Arquitectura

El diagrama de arriba resume el sistema de punta a punta.

Dos motores privados —uno de ingesta, uno de análisis— que comparten el mismo warehouse. La descarga de cada fuente corre con su propio manejo de rate-limits y caché incremental (no vuelve a traer lo que no cambió); el pricing y las señales se recalculan sobre esa foto ya consolidada, orquestados como un pipeline de 5 fases con Prefect.

## Cobertura de datos

**Economía real y variables de mercado — Argentina** (14 vistas): Cambiario · Reservas y liquidez · Fiscal · Monetario · Crédito y bancos · Actividad Real · PIB y Cuentas Nacionales · Empleo y Sociedad · Precios · Consumo y Retail · Sector Externo · Expectativas · Mercados · Correlaciones.

**Macro Argentina en detalle** (lo que un analista busca y cada vista trae resuelto):

| Vista | Qué incluye |
|---|---|
| 💵 **Cambiario** | Reservas brutas, riesgo país, "La Trinidad Macro" (inflación, tasa y crawling peg), cuadro de 6 dólares con brecha, ITCRM |
| 🏛️ **Reservas y liquidez** | Escalera de reservas brutas → netas tipo FMI → netas líquidas, composición oficial SDDS, liquidez a un año, compras y ventas diarias del BCRA |
| 🧾 **Fiscal** | Recaudación total y por impuesto (ARCA), resultado primario y financiero real y en % del PIB, deuda y financiamiento del Tesoro, previsional, cuasifiscal, tablero con calendario de publicaciones |
| 🏦 **Monetario** | BADLAR/TAMAR, M1/M2/M3 y base monetaria (nominal y real), balance del BCRA, pases, depósitos del gobierno, préstamos por tipo deflactados, tasas de depósitos e hipotecarias |
| 🏧 **Crédito y bancos** | Crédito por línea diario, crecimiento real, depósitos por titular y tasas, calidad de cartera por línea y grupo de entidades, liquidez, solvencia y rentabilidad |
| 📈 **Actividad Real** y 📊 **PIB** | EMAE (original, desestacionalizado, tendencia-ciclo), PIB por demanda y por sector, ISAC (construcción), servicios públicos, producción industrial |
| 👷 **Empleo y Sociedad** | Tasas de la EPH por región y aglomerado, brechas por género y edad, SMVM y RIPTE (nominal y real), empleo registrado (SIPA) por sector, tamaño y sexo, pobreza e indigencia, canasta básica y de crianza |
| 🛒 **Precios** | IPC por rubro, núcleo / estacional / regulados, por región, ponderaciones, precios mayoristas y costo de la construcción |
| 🛍️ **Consumo y Retail** | Supermercados, autoservicios mayoristas, centros de compras y electrodomésticos: ventas a precios constantes y corrientes, medios de pago, ventas por rubro |
| 🌎 **Sector Externo** | Comercio exterior por zona y por producto, complejos exportadores y origen provincial, turismo internacional, deuda externa bruta 2006-hoy |
| 🔮 **Expectativas** | REM (inflación, tipo de cambio, PIB, desocupación, fiscal), confianza del consumidor y del gobierno (UTDT), **track record** del REM y su **precisión** (sesgo y error por plazo) |
| 🌾 **Mercados** | S&P Merval, commodities agro y liquidación de divisas del agro |
| 🔗 **Correlaciones** | Matriz con horizonte elegible, dinero e inflación, brecha como predictor, riesgo país global vs local |

Cada vista muestra la **fecha del último dato de cada serie** y un semáforo de frescura; un footer permanente avisa si algo está desactualizado.

**Variables globales que mira el mercado**: bancos centrales (Fed, ECB, BOJ, PBOC) y su liquidez agregada · curvas del Tesoro americano y TIPS · inflación breakeven · índice de estrés sistémico (VIX, MOVE, VVIX, CISS) · curvas de recesión (T10Y2Y, T10Y3M) · fiscal de EE.UU. · ~65 activos de Yahoo Finance con barras de 30 minutos en horario de mercado y cualquier otro ticker a demanda.

**Universo de renta fija argentina**: Tasa fija, CER, Dollar Linked, Duales, Soberanos/Provinciales USD, 189 Obligaciones Negociables corporativas en USD (ley local y extranjera) y Corporativos/Provinciales en ARS a tasa fija o variable (TAMAR/BADLAR), con curva de rendimiento ajustada en vivo para cada clase de emisor (logarítmica, con su R² y error fuera de muestra a la vista), puntas bid/ask de IOL y la historia de precios (incluidos 101 instrumentos en pesos ya vencidos, para no tener sesgo de supervivencia).

**Fiscal y bancos, con fuente oficial y sin leer PDF**: recaudación y resultado (datos.gob.ar), recaudación por impuesto, boletín de seguridad social por sector, tamaño y sexo, y anuario de ARCA, deuda pública y colocaciones del Tesoro (Secretaría de Finanzas), crédito por línea y depósitos por titular (BCRA), anexo estadístico del Informe sobre Bancos y reservas internacionales con la planilla SDDS del FMI.

**Alcance real de las APIs oficiales**: BCRA y FRED exponen catálogos mucho más grandes de lo que el sistema integra por defecto, y el sistema no descarga todo lo que existe — integra lo que es relevante para el análisis. La API de BCRA se usa en las categorías Monetarias, Cambiarias, Transparencia (tasas por banco) y Planillas oficiales, más los saldos diarios de préstamos y depósitos y las series de reservas; el organismo expone además otras categorías de microdatos regulatorios (por ejemplo cheques rechazados, central de deudores del sistema financiero) que hoy quedan fuera del alcance del proyecto. La API de FRED tiene un catálogo total de ~800.000 series — el sistema mantiene curadas ~1.100 por relevancia temática, con el catálogo completo indexable bajo demanda sin necesidad de descargarlo por defecto. Los reportes técnicos de INDEC no se scrapean por scrapear: hay más de 25 módulos con parser propio, y cada uno se descarga solo el día que le corresponde según el calendario oficial de difusión (el plan semestral del INDEC más las fechas reales de publicación, ver Rigor de ingeniería), con un margen de ±1 día por si se publica antes o después.

## Pipeline de ejecución

El sistema corre en un orden fijo de 4 pasos, repartidos entre este repo y su capa de datos (ETL):

1. **Universo de bonos** — sincroniza el universo completo de Títulos Públicos, Obligaciones Negociables y Fideicomisos Financieros del MAE (no solo los "vigentes" que expone la API oficial), de forma incremental.
2. **Macro global** — descarga masiva desde BCRA, INDEC, FRED/ECB/BIS/Tesoro, datos.gob.ar, ARCA, Secretaría de Finanzas y la API del FMI, y arma el índice unificado de series. INDEC se gatea contra su calendario oficial de difusión — cada módulo se scrapea solo el día que corresponde, con margen de ±1 día por si se publica antes o después.
3. **Precios en vivo** — actualiza intradiario los futuros de dólar (scraping de fuentes públicas de mercado) y los precios y puntas de bonos/letras/ON vía IOL, con un contador de cuota mensual que corta antes de arriesgar el acceso a la API.
4. **Motor de pricing y dashboard** — corre el ETL de planillas BCRA/INDEC, dispara el pipeline de pricers (Prefect), archiva la foto intradía, exporta el book completo de Excel y levanta el escritorio en Streamlit.

Los pasos 2-4 corren también sin intervención humana, con **6 tareas programadas de Windows** (detalle más abajo). Un test automatizado verifica que la tarea programada corra exactamente el mismo pipeline que una corrida manual, no una aproximación.
## Las 6 tareas programadas

Corren con el Programador de tareas de Windows, sin intervención manual:

| Tarea | Cuándo | Qué hace |
|---|---|---|
| **Macro diaria** | Todos los días, 17:30 | Descarga las 14 fuentes (INDEC gateado por su calendario), regenera los archivos limpios y el índice de salud |
| **Precios, pricing y export** | Lunes a viernes, cada 30 minutos de 11:00 a 17:00 | Futuros de dólar, precios y puntas de IOL, recálculo de todos los pricers y export del libro completo de Excel |
| **Mercados globales** | Lunes a viernes, cada 30 minutos desde las 10:30 | Barras intradiarias de ~65 activos de Yahoo Finance |
| **Historia semanal** | Viernes, 18:00 | Historia de precios, reconstrucción de la TIR histórica, respaldo verificado (SHA-256) y chequeo de calidad de datos |
| **Lecturas y alertas** | Lunes a viernes, 18:15 | Arma las lecturas del día, avisa si alguna cambió y alimenta el registro de predicciones |
| **Vigilante** | Todos los días, 10:00 | Controla que la maquinaria diaria haya corrido; si algo quedó sin correr, lo avisa en la propia terminal |

## Versiones históricas de series revisadas

El INDEC y el BCRA **revisan sus series y no publican las versiones anteriores**. Para poder decir «qué se sabía cada día» (y probar un análisis sin mirar el futuro), el sistema guarda dos cosas:

- **ALFRED**: las versiones históricas oficiales de 14 series de la Fed que se revisan (empleo, producción industrial, desempleo, PIB, condiciones financieras, actividad nacional...): para cada período, cada valor y desde qué día estuvo publicado.
- **Versiones propias de las series argentinas** (EMAE, PIB, IPC, balanza, salarios, agregados monetarios, tipo de cambio real multilateral y fiscal): cada día se compara lo descargado contra lo guardado y se agrega una fila por cada período nuevo o revisado. La primera corrida es la línea de base: las revisiones se miden desde ahí, y se dice así.

## Índice maestro y salud de los datos

- Un **índice maestro** de todas las series (fuente, identificador, tema, unidad, frecuencia, primer y último dato, cantidad de observaciones y estado) que además marca cuándo una misma magnitud existe en más de una fuente (por ejemplo, la inflación en el BCRA, el INDEC y la Fed).
- Un **reporte de salud** con los problemas ordenados por gravedad (sin archivo, vacía, pocas observaciones, desactualizada) y umbrales de frescura por frecuencia: una serie anual desactualizada a los 14 meses es normal; una diaria, no. Una serie atrasada se compara contra lo último que publica el origen: si no hay nada más nuevo, se marca como **fuente parada** y no como un fallo de la descarga.
- Un **historial de corridas** (qué fuentes corrieron, cuáles fallaron, cuántas series y cuánto cambió respecto de la corrida anterior) y un comando de **verificación previa** que, sin bajar nada, valida que las series configuradas pertenezcan a su release, que las URLs de las planillas respondan y que el entorno tenga todas las dependencias.

## Cómo se arma la ficha de un bono

1. **Indexación** del universo de títulos públicos, obligaciones negociables y fideicomisos del MAE (incluidos los que su API no lista como vigentes).
2. **Descarga** de los PDF legales de cada emisión (avisos de resultados, suplementos, resoluciones) y **lectura** en paralelo, con reconocimiento óptico de caracteres en las páginas escaneadas.
3. **Fichado**: una ficha estructurada con el cronograma de pagos, la moneda, la indexación y la ley, armada **caso por caso con un protocolo escrito y asistencia de IA**. No se automatizó a propósito: un parser por expresiones regulares dejó un bono real con 26 cuotas trimestrales al 0 % en lugar de sus 42 cuotas mensuales.
4. **Verificación**: un verificador revisa el esquema, la coherencia moneda-indexación, los campos recomendados (ISIN, monto emitido, garantía) y marca las fichas sin completar; otro contrasta la ficha con el mercado (por ejemplo, un precio que «se cae» de golpe en una fecha de pago donde la ficha no tiene amortización).
5. **Inyección** del cronograma en los parámetros que lee el motor de pricing.

---
[← Volver al inicio del repositorio](../README.md)

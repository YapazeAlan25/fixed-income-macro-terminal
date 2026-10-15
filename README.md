# Terminal de Renta Fija y Macroeconomía Argentina

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-Dashboard-FF4B4B?logo=streamlit&logoColor=white)
![DuckDB](https://img.shields.io/badge/DuckDB-Warehouse-FFF000?logo=duckdb&logoColor=black)
![Prefect](https://img.shields.io/badge/Prefect-Orquestación-070E10?logo=prefect&logoColor=white)
![Estado](https://img.shields.io/badge/Estado-en%20producción%20diaria-2ea44f)
![Tests](https://img.shields.io/badge/Tests-~2.000%20automatizados-2ea44f)
![Código](https://img.shields.io/badge/Código-privado%20%7C%20repo%20de%20portfolio-lightgrey)

Escritorio de análisis financiero propio que cubre bonos argentinos (pricing, valor relativo, arbitraje y seguimiento de una cartera), la economía real de Argentina (actividad, empleo, precios, **fiscal, sistema bancario, reservas**, sector externo) y las variables globales que mueve el mercado (tasas americanas, estrés sistémico, curvas de recesión). No es solo un pricer de bonos: es una terminal completa, del tipo que un analista de mesa consulta todos los días para tener la foto completa antes de tomar una posición.

> 📌 **Este repositorio es una vidriera del proyecto.** Documenta su funcionamiento y resultados con capturas reales. El código fuente es privado; este README explica arquitectura, alcance y stack técnico.

**Autor:** Alan Yapaze — [LinkedIn](https://www.linkedin.com/in/alan-yapaze/)
**Vigencia:** 2023 – presente (iniciado como modelado manual en Excel, migrado en 2026 a una arquitectura Python automatizada)

## En 30 segundos

- **El problema:** el mercado de renta fija argentina se analiza hoy con planillas armadas a mano: precios, TIR y duration bono por bono, macro cruzada a ojo y sin trazabilidad de qué número salió de dónde.
- **Qué construí:** una terminal propia que descarga automáticamente 13 fuentes oficiales y de mercado, valúa más de 200 instrumentos con motores de pricing propios, cruza todo con la macro argentina y global y sigue una cartera real. Corre todos los días sin intervención.
- **Qué la distingue:** cada número se puede **defender**. Las comisiones del broker están en todos los precios (tres vistas: Mercado, Compra, Venta); cada análisis nuevo se contrasta contra una fuente independiente antes de mostrarse; los resultados negativos se publican (por ejemplo, ninguna señal técnica sobrevive a la corrección por pruebas múltiples); y casi 2.000 tests protegen los cálculos financieros.
- **Para quién es útil:** mesas de renta fija y trading, research, riesgo, asset management y tesorerías corporativas que operan deuda argentina.
- **Qué demuestra de mí:** finanzas cuantitativas aplicadas, ingeniería de datos de punta a punta, criterio estadístico y un proceso de verificación sistemático (ver [Qué demuestra este proyecto](#qué-demuestra-este-proyecto)).

<img src="assets/architecture.svg" alt="Arquitectura: fuentes de datos → motor de ingesta → warehouse DuckDB/Parquet → motores de pricing y capa de señales → dashboard Streamlit → entregables, con operación y calidad" width="100%" />

## Un vistazo en seis imágenes

Capturas reales de la última corrida del pricing (14/10/2026). Cada imagen lleva a su sección; el recorrido completo está en [Capturas](#capturas).

<table>
<tr><td width="50%" valign="top"><a href="#armador-con-optimización-de-markowitz--cartera-de-9-bonos-con-topes-por-emisor-y-por-sector"><img src="assets/screenshots/armador_markowitz_2_composicion_sugerida.webp" alt="Armador de portafolios con Markowitz: cartera de 9 bonos con topes por emisor y por sector, nominales y precio neto con comisiones" width="100%"></a><br><sub>Armador de portafolios con Markowitz: cartera de 9 bonos con topes por emisor y por sector, nominales y precio neto con comisiones</sub></td><td width="50%" valign="top"><a href="#ficha-de-bono-drill-down-completo-por-instrumento"><img src="assets/screenshots/ficha_hard_dollar_AL30_kpis_riesgo_vistas.webp" alt="Ficha de bono: KPIs, riesgo crediticio y las tres vistas de precio (Mercado, Compra y Venta)" width="100%"></a><br><sub>Ficha de bono: KPIs, riesgo crediticio y las tres vistas de precio (Mercado, Compra y Venta)</sub></td></tr>
<tr><td width="50%" valign="top"><a href="#tasa-fija-y-cer--curvas-de-rendimiento-y-tablas-de-pricing"><img src="assets/screenshots/rf_tasa_fija_kpis_curva_logaritmica.webp" alt="Curva logarítmica de Tasa Fija, con su R² y error fuera de muestra, y la rueda anterior superpuesta" width="100%"></a><br><sub>Curva logarítmica de Tasa Fija, con su R² y error fuera de muestra, y la rueda anterior superpuesta</sub></td><td width="50%" valign="top"><a href="#inicio--brief-matutino"><img src="assets/screenshots/inicio_brief_banderas_del_dia.webp" alt="Brief de apertura: banderas de riesgo macro global y riesgo Argentina" width="100%"></a><br><sub>Brief de apertura: banderas de riesgo macro global y riesgo Argentina</sub></td></tr>
<tr><td width="50%" valign="top"><a href="#salud-de-datos-y-reloj-de-mercados"><img src="assets/screenshots/inicio_reloj_mercados_del_mundo.webp" alt="Reloj de mercados del mundo con solapamientos de rueda" width="100%"></a><br><sub>Reloj de mercados del mundo con solapamientos de rueda</sub></td><td width="50%" valign="top"><a href="#ejemplos-de-fichas-exportadas"><img src="assets/screenshots/excel_ficha_hard_dollar_GD46_metricas_vistas.webp" alt="Ficha Excel por instrumento: métricas y vistas de precio, lista para un research desk" width="100%"></a><br><sub>Ficha Excel por instrumento: métricas y vistas de precio, lista para un research desk</sub></td></tr>
</table>

## Índice

- [En 30 segundos](#en-30-segundos)
- [Un vistazo en seis imágenes](#un-vistazo-en-seis-imágenes)
- [El proyecto en números](#el-proyecto-en-números)
- [Novedades de septiembre-octubre 2026](#novedades-de-septiembre-octubre-2026)
- [Qué es](#qué-es)
- [Arquitectura](#arquitectura)
- [Cobertura de datos](#cobertura-de-datos)
- [Pipeline de ejecución](#pipeline-de-ejecución)
- [Capturas](#capturas)
- [Qué resuelve](#qué-resuelve)
- [Un día de trabajo de un analista](#un-día-de-trabajo-de-un-analista)
- [Motores de pricing](#motores-de-pricing)
- [Comisiones y vistas Mercado / Compra / Venta](#comisiones-y-vistas-mercado--compra--venta)
- [Armador de portafolios: Markowitz con restricciones reales](#armador-de-portafolios-markowitz-con-restricciones-reales)
- [Terminal de analista: valor relativo, TIR histórica y señales](#terminal-de-analista-valor-relativo-tir-histórica-y-señales)
- [Fiscal, sistema bancario y reservas con datos oficiales](#fiscal-sistema-bancario-y-reservas-con-datos-oficiales)
- [Mi cartera: tenencias, riesgo y calificaciones](#mi-cartera-tenencias-riesgo-y-calificaciones)
- [Exportación a Excel: fichas de nivel research desk](#exportación-a-excel-fichas-de-nivel-research-desk)
- [Otros motores destacados](#otros-motores-destacados)
- [Hallazgos de análisis (con sus límites)](#hallazgos-de-análisis-con-sus-límites)
- [Rigor de ingeniería y control de calidad](#rigor-de-ingeniería-y-control-de-calidad)
- [Qué demuestra este proyecto](#qué-demuestra-este-proyecto)
- [Lógica destacada (ejemplo ilustrativo)](#lógica-destacada-ejemplo-ilustrativo)
- [Stack técnico](#stack-técnico)
- [Roadmap](#roadmap)
- [Contacto](#contacto)

## El proyecto en números

| | |
|---|---|
| **13** | fuentes de descarga automática en paralelo (BCRA, INDEC, FRED, ECB, BIS, Tesoro de EE.UU., datos.gob.ar, ARCA, Secretaría de Finanzas, FMI, UTDT, mercados AR y yfinance), más un índice unificado de series; a eso se suman precios en vivo e históricos de IOL y de fuentes públicas de mercado, los prospectos del MAE y las calificaciones de la CNV |
| **200+** | instrumentos de renta fija cubiertos (36 soberanos/provinciales + 189 Obligaciones Negociables corporativas + letras y bonos en pesos) |
| **7** | tipos de instrumento con motor de pricing propio, cada uno en **tres vistas de precio** (Mercado, Compra y Venta, con comisiones del broker) |
| **5** | secciones del escritorio: Inicio, Renta Fija (16 pestañas), Liquidez Global (5 pestañas), Macro Argentina (14 vistas) y Mi cartera |
| **~460** | funciones de acceso a datos sobre el warehouse (una por serie o grupo de series) |
| **~30** | módulos con parser propio para los Excel oficiales de INDEC |
| **~2.000** | tests automatizados en las dos capas del sistema (1.859 sobre el motor de pricing y el dashboard + 180 sobre el motor de descarga): desde imports y parsing hasta la reconciliación de cada ficha Excel contra el precio de mercado, el dashboard en vivo con datos reales y controles cruzados contra fuentes independientes. Se verifica además que la tarea programada corra exactamente el mismo pipeline que una corrida manual |
| **4** | tareas programadas (Windows Task Scheduler) que mantienen datos, precios, mercados e historia al día sin intervención manual |
| **~1m 40s** | descarga completa de las fuentes en paralelo (medido con 13 fuentes) |
| **21s** | pipeline de pricing completo (motor de pricing + carga a warehouse), 3.5x más rápido tras una optimización de la etapa de ingesta |
| **< 1 s** | tiempo por clic en cualquier vista del escritorio fiscal y bancario ya visitada (medido pantalla por pantalla; la vista de deuda pasó de 95 s a 15 s la primera vez y 0,2 s después, ver [Rigor de ingeniería](#rigor-de-ingeniería-y-control-de-calidad)) |

## Novedades de septiembre-octubre 2026

La versión anterior de este README documentaba el pricer y el escritorio macro. Desde entonces el sistema creció en seis frentes:

- **Fiscal y sistema bancario con fuentes oficiales**: tres vistas nuevas de Macro Argentina — 🧾 Fiscal (9 sub-vistas: recaudación por impuesto de ARCA, resultado, deuda y financiamiento, previsional, cuasifiscal, empleo y salarios del SIPA con cuadros por sexo), 🏧 Crédito y bancos (crédito por línea, crecimiento real, depósitos y tasas, calidad de cartera, solvencia) y 🏛️ Reservas y liquidez (escalera de reservas netas verificada contra el FMI). Ver [Fiscal, sistema bancario y reservas](#fiscal-sistema-bancario-y-reservas-con-datos-oficiales).
- **Renta fija como terminal de analista**: comisiones del broker y vistas Mercado/Compra/Venta en todos los pricers, TIR histórica reconstruida con los pricers reales, valor relativo con estudio de eventos, forwards implícitas y roll-down, MEP/brecha, carry trade como operación completa y un resumen "qué cambió hoy". Ver [Comisiones](#comisiones-y-vistas-mercado--compra--venta) y [Terminal de analista](#terminal-de-analista-valor-relativo-tir-histórica-y-señales).
- **Mi cartera**: diario de operaciones, tenencias con TIR del dinero y FIFO, riesgo y estrés, calificaciones de las agencias locales leídas de la CNV, swing trading en renta fija con backtest honesto, optimizador y alertas. Ver [Mi cartera](#mi-cartera-tenencias-riesgo-y-calificaciones).
- **Liquidez global**: Charting estilo TradingView con niveles de soporte y resistencia, perfil de volumen, divergencias y un backtest de señales que informa lo que **no** funciona.
- **Menos PDF, más datos estructurados**: las reservas se leen de la API de datos del FMI y la calidad de cartera del anexo Excel del BCRA; los PDF quedaron como control opcional. Los análisis nuevos se contrastan contra la fuente independiente antes de mostrarse.
- **Operación robusta**: archivo intradía, respaldo semanal verificado, panel de salud de datos, reloj de mercados y caché de lecturas.
- **Datos siempre al día, con control propio**: cuando el INDEC renombró el Excel del IPC, la descarga siguió «OK» y las series se quedaron un mes atrás sin que ningún control lo viera. Ahora (1) el scraper compara en cada visita lo que la página publica contra lo guardado y avisa de archivos renombrados, nuevos o desaparecidos; (2) un control de frescura compara el último período publicado en el calendario real de difusión con el último período de las series de **23 módulos del INDEC** (hoy 23/23 al día); (3) los módulos con informe reciente se reintentan hasta que se vean los archivos; y (4) un aviso de «dato vencido» mide el rezago desde el cierre del período, no desde su primer día, para no alarmar con datos que están al día. Cada hallazgo de este tipo quedó con su test.

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

**Alcance real de las APIs oficiales**: BCRA y FRED exponen catálogos mucho más grandes de lo que el sistema integra por defecto, y el sistema no descarga todo lo que existe — integra lo que es relevante para el análisis. La API de BCRA se usa en las categorías Monetarias, Cambiarias, Transparencia (tasas por banco) y Planillas oficiales, más los saldos diarios de préstamos y depósitos y las series de reservas; el organismo expone además otras categorías de microdatos regulatorios (por ejemplo cheques rechazados, central de deudores del sistema financiero) que hoy quedan fuera del alcance del proyecto. La API de FRED tiene un catálogo total de ~800.000 series — el sistema mantiene curadas ~1.100 por relevancia temática, con el catálogo completo indexable bajo demanda sin necesidad de descargarlo por defecto. Los reportes técnicos de INDEC no se scrapean por scrapear: hay más de 25 módulos con parser propio, y cada uno se descarga solo el día que le corresponde según el calendario oficial de difusión (el plan semestral del INDEC más las fechas reales de publicación, ver [Rigor de ingeniería](#rigor-de-ingeniería-y-control-de-calidad)), con un margen de ±1 día por si se publica antes o después.

## Pipeline de ejecución

El sistema corre en un orden fijo de 4 pasos, repartidos entre este repo y su capa de datos (ETL):

1. **Universo de bonos** — sincroniza el universo completo de Títulos Públicos, Obligaciones Negociables y Fideicomisos Financieros del MAE (no solo los "vigentes" que expone la API oficial), de forma incremental.
2. **Macro global** — descarga masiva desde BCRA, INDEC, FRED/ECB/BIS/Tesoro, datos.gob.ar, ARCA, Secretaría de Finanzas y la API del FMI, y arma el índice unificado de series. INDEC se gatea contra su calendario oficial de difusión — cada módulo se scrapea solo el día que corresponde, con margen de ±1 día por si se publica antes o después.
3. **Precios en vivo** — actualiza intradiario los futuros de dólar (scraping de fuentes públicas de mercado) y los precios y puntas de bonos/letras/ON vía IOL, con un contador de cuota mensual que corta antes de arriesgar el acceso a la API.
4. **Motor de pricing y dashboard** — corre el ETL de planillas BCRA/INDEC, dispara el pipeline de pricers (Prefect), archiva la foto intradía, exporta el book completo de Excel y levanta el escritorio en Streamlit.

Los pasos 2-4 corren también sin intervención humana vía **4 tareas programadas de Windows**: la macro diaria; precios, pricing y export cada 30 minutos en horario de mercado; mercados globales cada 30 minutos; y una semanal (viernes) que baja la historia de precios, reconstruye la TIR histórica, hace un **respaldo verificado** con manifiesto SHA-256 y corre el chequeo de calidad de datos. Un test automatizado verifica que la tarea programada corra exactamente el mismo pipeline que una corrida manual, no una aproximación.

## Capturas

> **Capturas actualizadas al 14/10/2026**, tomadas de la **última corrida del pricing**: datos reales del sistema en funcionamiento, sin datos de ejemplo. Las imágenes se abren a tamaño completo; las tablas y gráficos se leen mejor ampliados.

**Ir directo a:**

- **Renta Fija**: [Tasa Fija y CER](#tasa-fija-y-cer--curvas-de-rendimiento-y-tablas-de-pricing) · [Dollar Linked](#dollar-linked--bandas-cambiarias-y-proyección-por-escenario) · [Soberanos y Provinciales USD](#soberanos-y-provinciales-usd--mep-implícito-y-arbitraje-legislativo) · [Corporativos USD](#corporativos-usd--189-obligaciones-negociables-por-ley-y-por-sector) · [Ficha de bono](#ficha-de-bono-drill-down-completo-por-instrumento) · [Tablero de Arbitraje](#tablero-de-arbitraje-señales-cuantitativas-de-trading) · [Carry Trade](#carry-trade--breakeven-de-devaluación-vs-expectativas-de-mercado) · [Qué cambió hoy y Tasa Fija con forwards y roll-down](#qué-cambió-hoy-y-tasa-fija-con-forwards-y-roll-down) · [Valor Relativo](#valor-relativo--spreads-de-legislación-con-estudio-de-eventos) · [UST vs Soberanos, MEP/Brecha y Carry Trade completo](#ust-vs-soberanos-mepbrecha-y-carry-trade-completo) · [Armador de Portafolios, Radar y Simulador de Posición](#armador-de-portafolios-radar-y-simulador-de-posición) · [Armador con optimización de Markowitz](#armador-con-optimización-de-markowitz--cartera-de-9-bonos-con-topes-por-emisor-y-por-sector) · [Comisiones del broker y vistas Mercado / Compra / Venta](#comisiones-del-broker-y-vistas-mercado--compra--venta) · [Matriz de Sensibilidad](#matriz-de-sensibilidad-tir-breakeven-total-return)
- **Macro Argentina**: [Inicio](#inicio--brief-matutino) · [Escritorio Macro Argentina](#escritorio-macro-argentina--panel-general) · [Cambiario](#cambiario--brecha-y-evolución-de-tipos-de-cambio) · [Inflación (IPC) por rubro](#inflación-ipc-por-rubro) · [Monetario](#monetario--agregados-y-tasas-de-referencia) · [Consumo y Retail](#consumo-y-retail--medios-de-pago) · [Reservas y liquidez](#reservas-y-liquidez--de-las-reservas-brutas-a-las-netas) · [Fiscal](#fiscal--recaudación-resultado-deuda-y-seguridad-social) · [Empleo y salarios (SIPA)](#empleo-y-salarios-sipa--mujeres-y-varones-y-destino-de-la-seguridad-social) · [Crédito y bancos](#crédito-y-bancos--crédito-depósitos-y-calidad-de-cartera) · [Correlaciones y análisis de evidencia](#correlaciones-y-análisis-de-evidencia) · [Más módulos de Macro Argentina](#más-módulos-de-macro-argentina--actividad-pib-empleo-y-sociedad-precios-sector-externo-expectativas-y-mercados)
- **Liquidez Global**: [Semáforo de estrés sistémico global (GSSI) y tasas americanas](#semáforo-de-estrés-sistémico-global-gssi-y-tasas-americanas) · [Mercados globales, Macro EE.UU. y economía real](#mercados-globales-macro-eeuu-y-economía-real) · [Charting estilo TradingView, niveles y backtest honesto de señales](#charting-estilo-tradingview-niveles-y-backtest-honesto-de-señales)
- **Cartera y operación**: [Mi cartera](#mi-cartera--tenencias-riesgo-y-calificaciones) · [Salud de datos y reloj de mercados](#salud-de-datos-y-reloj-de-mercados)
- **Entregables**: [Fichas Excel, archivos Global y Research Report en PDF](#ejemplos-de-fichas-exportadas)

### Inicio — brief matutino
El primer panel que ve el usuario al abrir el sistema: banderas de riesgo (macro global y riesgo Argentina) con las señales que las componen, cuadro de los tipos de cambio con brecha contra el oficial, mercado local y riesgo país, renta fija en foco y contexto overnight (futuros de EE.UU., mercados asiáticos, commodities agro).

<p align="center"><img src="assets/screenshots/inicio_brief_banderas_del_dia.webp" alt="Brief de apertura: banderas de riesgo (Macro Global y Riesgo Argentina) con las señales que las componen" width="100%"><br><sub>Brief de apertura: banderas de riesgo (Macro Global y Riesgo Argentina) con las señales que las componen</sub></p>

<p align="center"><img src="assets/screenshots/inicio_brief_cuadro_cambiario.webp" alt="Cuadro cambiario completo con brecha contra el oficial" width="100%"><br><sub>Cuadro cambiario completo con brecha contra el oficial</sub></p>

<p align="center"><img src="assets/screenshots/inicio_brief_mercado_local_y_riesgo_argentina.webp" alt="Mercado local y riesgo Argentina" width="100%"><br><sub>Mercado local y riesgo Argentina</sub></p>

<p align="center"><img src="assets/screenshots/inicio_brief_renta_fija_en_foco.webp" alt="Renta fija argentina en foco" width="100%"><br><sub>Renta fija argentina en foco</sub></p>

<p align="center"><img src="assets/screenshots/inicio_brief_contexto_overnight_y_agro.webp" alt="Contexto overnight (futuros, Asia) y commodities agro" width="100%"><br><sub>Contexto overnight (futuros, Asia) y commodities agro</sub></p>

### Escritorio Macro Argentina — panel general
Estado de reservas, tasa real, brecha cambiaria, riesgo país, resultado fiscal, actividad y desempleo en un solo panel; reservas del BCRA y riesgo país (EMBI+) con historia completa.

<p align="center"><img src="assets/screenshots/macro_panel_general_kpis.webp" alt="Escritorio Macro Argentina: semáforos de reservas, tasa real, brecha CCL, riesgo país, actividad, cobertura de reservas y desempleo, con el selector de las 14 secciones" width="100%"><br><sub>Escritorio Macro Argentina: semáforos de reservas, tasa real, brecha CCL, riesgo país, actividad, cobertura de reservas y desempleo, con el selector de las 14 secciones</sub></p>

<p align="center"><img src="assets/screenshots/macro_cambiario_reservas_y_riesgo_pais.webp" alt="Reservas internacionales brutas del BCRA y riesgo país (EMBI+) comparado con la región" width="100%"><br><sub>Reservas internacionales brutas del BCRA y riesgo país (EMBI+) comparado con la región</sub></p>

### Cambiario — brecha y evolución de tipos de cambio
"La Trinidad Macro" (inflación mensual, tasa de interés y crawling peg en un mismo eje), brecha CCL/oficial histórica, y evolución completa de los distintos tipos de cambio desde convertibilidad.

<p align="center"><img src="assets/screenshots/macro_cambiario_trinidad_macro.webp" alt="Cambiario: la trinidad macro (inflación, tasa y crawling peg) y la brecha CCL contra el oficial" width="100%"><br><sub>Cambiario: la trinidad macro (inflación, tasa y crawling peg) y la brecha CCL contra el oficial</sub></p>

<p align="center"><img src="assets/screenshots/macro_cambiario_evolucion_tipos_de_cambio.webp" alt="Cambiario: evolución de los tipos de cambio (oficial, mayorista, blue, MEP, CCL, cripto) y brecha de cada uno contra el oficial" width="100%"><br><sub>Cambiario: evolución de los tipos de cambio (oficial, mayorista, blue, MEP, CCL, cripto) y brecha de cada uno contra el oficial</sub></p>

<p align="center"><img src="assets/screenshots/macro_cambiario_regimen_de_bandas.webp" alt="Cambiario: régimen de bandas cambiarias con techo y piso oficiales y proyectados" width="100%"><br><sub>Cambiario: régimen de bandas cambiarias con techo y piso oficiales y proyectados</sub></p>

<p align="center"><img src="assets/screenshots/macro_cambiario_competitividad_itcrm.webp" alt="Cambiario: tipo de cambio real multilateral (ITCRM), bilateral por socio y cotizaciones de referencia del BCRA" width="100%"><br><sub>Cambiario: tipo de cambio real multilateral (ITCRM), bilateral por socio y cotizaciones de referencia del BCRA</sub></p>

<p align="center"><img src="assets/screenshots/macro_cambiario_carry_trade.webp" alt="Cambiario: monitor de carry trade, diferencial entre la tasa TAMAR y la tasa de la Fed" width="100%"><br><sub>Cambiario: monitor de carry trade, diferencial entre la tasa TAMAR y la tasa de la Fed</sub></p>

### Inflación (IPC) por rubro
Serie histórica completa del IPC (incluye la hiperinflación de 1989-90), apertura por rubro y comparación núcleo vs. estacional vs. regulados — construido sobre el parser propio de los Excel de INDEC.

<p align="center"><img src="assets/screenshots/macro_precios_ipc_resumen_ejecutivo.webp" alt="Precios: IPC de septiembre de 2026, resumen ejecutivo con los destacados del mes" width="100%"><br><sub>Precios: IPC de septiembre de 2026, resumen ejecutivo con los destacados del mes</sub></p>

<p align="center"><img src="assets/screenshots/macro_precios_ipc_historico_nivel_general.webp" alt="Precios: IPC histórico completo, nivel general y categorías (incluye la hiperinflación de 1989-90)" width="100%"><br><sub>Precios: IPC histórico completo, nivel general y categorías (incluye la hiperinflación de 1989-90)</sub></p>

<p align="center"><img src="assets/screenshots/macro_precios_ipc_aperturas.webp" alt="Precios: IPC por divisiones, bienes y servicios, categorías y aperturas regionales" width="100%"><br><sub>Precios: IPC por divisiones, bienes y servicios, categorías y aperturas regionales</sub></p>

<p align="center"><img src="assets/screenshots/macro_precios_ipc_dinamica_de_precios.webp" alt="Precios: dinámica de precios, núcleo vs. estacional vs. regulados" width="100%"><br><sub>Precios: dinámica de precios, núcleo vs. estacional vs. regulados</sub></p>

### Monetario — agregados y tasas de referencia
M1/M2/M3 y base monetaria, costo del dinero, pasivos remunerados y balance del BCRA, sistema financiero, tasas y costo financiero, radar micro-bancario y mercado cambiario: el tablero de política monetaria completo. Los agregados mensuales dicen cuándo se espera el próximo dato oficial.

<p align="center"><img src="assets/screenshots/macro_monetario_panorama.webp" alt="Monetario: panorama con costo del dinero, agregados monetarios oficiales, balance del BCRA y pasivos remunerados" width="100%"><br><sub>Monetario: panorama con costo del dinero, agregados monetarios oficiales, balance del BCRA y pasivos remunerados</sub></p>

<p align="center"><img src="assets/screenshots/macro_monetario_sistema_financiero.webp" alt="Monetario: sistema financiero (préstamos, depósitos y liquidez)" width="100%"><br><sub>Monetario: sistema financiero (préstamos, depósitos y liquidez)</sub></p>

<p align="center"><img src="assets/screenshots/macro_monetario_tasas_y_costo_financiero.webp" alt="Monetario: tasas y costo financiero (BADLAR, TAMAR y tasas de préstamos)" width="100%"><br><sub>Monetario: tasas y costo financiero (BADLAR, TAMAR y tasas de préstamos)</sub></p>

<p align="center"><img src="assets/screenshots/macro_monetario_radar_micro_bancario.webp" alt="Monetario: radar micro-bancario por entidad" width="100%"><br><sub>Monetario: radar micro-bancario por entidad</sub></p>

<p align="center"><img src="assets/screenshots/macro_monetario_mercado_cambiario.webp" alt="Monetario: mercado cambiario (compras y ventas del BCRA, volumen y posición)" width="100%"><br><sub>Monetario: mercado cambiario (compras y ventas del BCRA, volumen y posición)</sub></p>

### Consumo y Retail — medios de pago
Encuestas de supermercados, autoservicios mayoristas y centros de compras del INDEC: índice de ventas a precios constantes, cuadros del informe y gráficos de serie histórica completa.

<p align="center"><img src="assets/screenshots/macro_consumo_supermercados.webp" alt="Consumo: encuesta de supermercados (ventas a precios constantes, variación interanual y cuadros)" width="100%"><br><sub>Consumo: encuesta de supermercados (ventas a precios constantes, variación interanual y cuadros)</sub></p>

<p align="center"><img src="assets/screenshots/macro_consumo_autoservicios_mayoristas.webp" alt="Consumo: encuesta de autoservicios mayoristas (índice de ventas a precios constantes y cuadros del informe)" width="100%"><br><sub>Consumo: encuesta de autoservicios mayoristas (índice de ventas a precios constantes y cuadros del informe)</sub></p>

<p align="center"><img src="assets/screenshots/macro_consumo_centros_de_compras.webp" alt="Consumo: encuesta nacional de centros de compras" width="100%"><br><sub>Consumo: encuesta nacional de centros de compras</sub></p>

### Reservas y liquidez — de las reservas brutas a las netas
La "escalera" de reservas: brutas → netas de encajes → **netas tipo FMI** (descontando el swap con China, la línea del BIS, SEDESA y las compras al Fondo) → netas líquidas. La versión tipo FMI reproduce el dato que publica el propio FMI con una diferencia de USD 27 y 60 millones a fines de 2024 y 2025. Incluye la composición oficial de la planilla SDDS, la liquidez a un año y las compras y ventas diarias de dólares del BCRA.

<p align="center"><img src="assets/screenshots/macro_reservas_escalera_brutas_a_netas.webp" alt="Reservas: la escalera de reservas brutas a netas, netas tipo FMI y netas líquidas" width="100%"><br><sub>Reservas: la escalera de reservas brutas a netas, netas tipo FMI y netas líquidas</sub></p>

<p align="center"><img src="assets/screenshots/macro_reservas_compras_y_ventas_bcra.webp" alt="Reservas: compras y ventas diarias de dólares del BCRA" width="100%"><br><sub>Reservas: compras y ventas diarias de dólares del BCRA</sub></p>

<p align="center"><img src="assets/screenshots/macro_reservas_composicion_sdds_y_nir_fmi.webp" alt="Reservas: composición oficial de la planilla SDDS, liquidez a un año y contraste con el NIR del FMI" width="100%"><br><sub>Reservas: composición oficial de la planilla SDDS, liquidez a un año y contraste con el NIR del FMI</sub></p>

<p align="center"><img src="assets/screenshots/macro_reservas_factores_de_variacion.webp" alt="Reservas: por qué cambian las reservas brutas (factores de variación del BCRA)" width="100%"><br><sub>Reservas: por qué cambian las reservas brutas (factores de variación del BCRA)</sub></p>

<p align="center"><img src="assets/screenshots/macro_reservas_cobertura_y_dolarizacion.webp" alt="Reservas: cobertura de la base monetaria ampliada y dolarización de los depósitos" width="100%"><br><sub>Reservas: cobertura de la base monetaria ampliada y dolarización de los depósitos</sub></p>

### Fiscal — recaudación, resultado, deuda y seguridad social
Nueve sub-vistas con datos oficiales: tablero con calendario de publicaciones, resumen fiscal (resultado primario/financiero en pesos reales y % del PIB), recaudación por impuesto de ARCA, deuda y financiamiento (stock por moneda e instrumento, perfil de vencimientos, colocaciones del Tesoro, tenedores), previsional, cuasifiscal, empleo y salarios del SIPA y largo plazo por región. Cada serie lleva su fecha de última actualización y controles de identidad (por ejemplo, DGI + DGA + seguridad social = recaudación total).

<p align="center"><img src="assets/screenshots/macro_fiscal_tablero_y_calendario.webp" alt="Fiscal: tablero fiscal-financiero y calendario de publicaciones" width="100%"><br><sub>Fiscal: tablero fiscal-financiero y calendario de publicaciones</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_resumen_fiscal.webp" alt="Fiscal: resumen fiscal con resultado primario y financiero en pesos reales y como % del PIB" width="100%"><br><sub>Fiscal: resumen fiscal con resultado primario y financiero en pesos reales y como % del PIB</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_recaudacion_por_impuesto_arca.webp" alt="Fiscal: recaudación por impuesto (ARCA), acumulado, 12 meses y último mes" width="100%"><br><sub>Fiscal: recaudación por impuesto (ARCA), acumulado, 12 meses y último mes</sub></p>

<details>
<summary>📷 Ver las 5 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/macro_fiscal_recaudacion_y_actividad.webp" alt="Fiscal: recaudación y actividad económica" width="100%"><br><sub>Fiscal: recaudación y actividad económica</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_deuda_y_financiamiento.webp" alt="Fiscal: deuda y financiamiento (stock por moneda e instrumento, vencimientos y colocaciones del Tesoro)" width="100%"><br><sub>Fiscal: deuda y financiamiento (stock por moneda e instrumento, vencimientos y colocaciones del Tesoro)</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_previsional.webp" alt="Fiscal: sistema previsional (haberes, jubilados y gasto)" width="100%"><br><sub>Fiscal: sistema previsional (haberes, jubilados y gasto)</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_cuasifiscal_y_credito.webp" alt="Fiscal: resultado cuasifiscal y crédito al sector público" width="100%"><br><sub>Fiscal: resultado cuasifiscal y crédito al sector público</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_largo_plazo_y_regiones.webp" alt="Fiscal: largo plazo y regiones (recaudación por jurisdicción)" width="100%"><br><sub>Fiscal: largo plazo y regiones (recaudación por jurisdicción)</sub></p>

</details>

### Empleo y salarios (SIPA) — mujeres y varones, y destino de la seguridad social
Empleo registrado y salarios reales del SIPA; el boletín de ARCA abierto por **sexo y sector de actividad** (las mujeres son ~38% de los trabajadores registrados y su salario bruto promedio es ~79% del de los varones — sin controlar horas ni puestos, por eso se muestra también por sector) y el **destino de la recaudación de la seguridad social** (ANSES 63%, PAMI 9%, obras sociales y ART 25%), que explica por qué la recaudación total de ARCA no es recaudación fiscal.

<p align="center"><img src="assets/screenshots/macro_fiscal_sipa_empleo_y_salarios.webp" alt="Fiscal: empleo registrado y salarios reales del SIPA" width="100%"><br><sub>Fiscal: empleo registrado y salarios reales del SIPA</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_sipa_mujeres_y_varones.webp" alt="Fiscal: trabajadores registrados por sexo y salario relativo por sector" width="100%"><br><sub>Fiscal: trabajadores registrados por sexo y salario relativo por sector</sub></p>

<p align="center"><img src="assets/screenshots/macro_fiscal_sipa_seguridad_social_conceptos_y_destino.webp" alt="Fiscal: recaudación de la seguridad social por concepto y destino (ANSES, PAMI, obras sociales y ART)" width="100%"><br><sub>Fiscal: recaudación de la seguridad social por concepto y destino (ANSES, PAMI, obras sociales y ART)</sub></p>

### Crédito y bancos — crédito, depósitos y calidad de cartera
Cinco secciones: **crédito por línea** con saldos diarios (adelantos, documentos, hipotecarios, prendarios, personales, tarjetas), **crecimiento real** de crédito y depósitos, **depósitos por titular y tasas** (un tercio de los depósitos privados en pesos son de fondos comunes de inversión), **calidad de cartera** (irregularidad por línea y por grupo de entidades desde 2010) y **liquidez, solvencia y rentabilidad** por grupo de bancos. Las identidades contables se verifican en cada lectura.

<p align="center"><img src="assets/screenshots/macro_credito_por_linea.webp" alt="Crédito y bancos: crédito por línea (adelantos, documentos, hipotecarios, prendarios, personales y tarjetas)" width="100%"><br><sub>Crédito y bancos: crédito por línea (adelantos, documentos, hipotecarios, prendarios, personales y tarjetas)</sub></p>

<p align="center"><img src="assets/screenshots/macro_credito_crecimiento_real.webp" alt="Crédito y bancos: crecimiento real de crédito y depósitos, en pesos y en dólares" width="100%"><br><sub>Crédito y bancos: crecimiento real de crédito y depósitos, en pesos y en dólares</sub></p>

<p align="center"><img src="assets/screenshots/macro_credito_depositos_y_tasas.webp" alt="Crédito y bancos: depósitos por titular y tasas" width="100%"><br><sub>Crédito y bancos: depósitos por titular y tasas</sub></p>

<p align="center"><img src="assets/screenshots/macro_credito_calidad_de_cartera.webp" alt="Crédito y bancos: calidad de cartera, irregularidad por línea y por grupo de entidades" width="100%"><br><sub>Crédito y bancos: calidad de cartera, irregularidad por línea y por grupo de entidades</sub></p>

<p align="center"><img src="assets/screenshots/macro_credito_liquidez_solvencia_rentabilidad.webp" alt="Crédito y bancos: liquidez, solvencia y rentabilidad por grupo de bancos" width="100%"><br><sub>Crédito y bancos: liquidez, solvencia y rentabilidad por grupo de bancos</sub></p>

### Correlaciones y análisis de evidencia
Matriz de correlaciones macro con horizonte elegible, y cinco análisis diseñados para no engañarse (dinero e inflación, brecha como predictor, riesgo país global vs local, precisión del REM). Cada resultado informa n, el estadístico, el azar esperable y corrige por pruebas múltiples. Ver [Hallazgos de análisis](#hallazgos-de-análisis-con-sus-límites).

<p align="center"><img src="assets/screenshots/macro_correlaciones_matriz.webp" alt="Correlaciones: matriz de agregados monetarios, reservas, brecha y riesgo país con horizonte elegible" width="100%"><br><sub>Correlaciones: matriz de agregados monetarios, reservas, brecha y riesgo país con horizonte elegible</sub></p>

<p align="center"><img src="assets/screenshots/macro_correlaciones_un_par_en_detalle.webp" alt="Correlaciones: un par en detalle, con correlación cruzada (quién adelanta a quién) y evolución móvil" width="100%"><br><sub>Correlaciones: un par en detalle, con correlación cruzada (quién adelanta a quién) y evolución móvil</sub></p>

<p align="center"><img src="assets/screenshots/macro_correlaciones_dinero_e_inflacion.webp" alt="Correlaciones: dinero e inflación, con n, estadístico y corrección por pruebas múltiples" width="100%"><br><sub>Correlaciones: dinero e inflación, con n, estadístico y corrección por pruebas múltiples</sub></p>

<details>
<summary>📷 Ver las 3 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/macro_correlaciones_brecha_como_predictor.webp" alt="Correlaciones: ¿la brecha cambiaria predice el riesgo país? con la prueba de robustez por horizonte y período" width="100%"><br><sub>Correlaciones: ¿la brecha cambiaria predice el riesgo país? con la prueba de robustez por horizonte y período</sub></p>

<p align="center"><img src="assets/screenshots/macro_correlaciones_riesgo_pais_global_vs_local.webp" alt="Correlaciones: riesgo país global vs. local" width="100%"><br><sub>Correlaciones: riesgo país global vs. local</sub></p>

<p align="center"><img src="assets/screenshots/macro_correlaciones_precision_del_rem.webp" alt="Correlaciones: precisión del REM, qué tan bien acertaron los analistas" width="100%"><br><sub>Correlaciones: precisión del REM, qué tan bien acertaron los analistas</sub></p>

</details>

### Más módulos de Macro Argentina — actividad, PIB, empleo y sociedad, precios, sector externo, expectativas y mercados
El escritorio cubre además la actividad (EMAE, industria, construcción), las cuentas nacionales (PIB, capacidad instalada, generación del ingreso), el mercado de trabajo, salarios, canastas, pobreza y distribución del ingreso de la EPH, los precios mayoristas y el costo de la construcción, el sector externo (intercambio comercial, balanza de pagos, turismo), el REM con su *track record* y el Merval con los commodities del agro. Cada informe del INDEC se puede ver «a la fecha» de cualquier publicación anterior.

<details>
<summary><b>Actividad real</b> (3 capturas)</summary>

<p align="center"><img src="assets/screenshots/macro_actividad_emae.webp" alt="Actividad real: EMAE con resumen ejecutivo, nivel general, cuadros del informe y mapa de calor sectorial" width="100%"><br><sub>Actividad real: EMAE con resumen ejecutivo, nivel general, cuadros del informe y mapa de calor sectorial</sub></p>

<p align="center"><img src="assets/screenshots/macro_actividad_ipi_manufacturero.webp" alt="Actividad real: índice de producción industrial manufacturero por rubro (variación mensual, interanual y acumulada)" width="100%"><br><sub>Actividad real: índice de producción industrial manufacturero por rubro (variación mensual, interanual y acumulada)</sub></p>

<p align="center"><img src="assets/screenshots/macro_actividad_isac_construccion.webp" alt="Actividad real: indicador sintético de la actividad de la construcción (ISAC) con los cuadros del informe" width="100%"><br><sub>Actividad real: indicador sintético de la actividad de la construcción (ISAC) con los cuadros del informe</sub></p>

</details>

<details>
<summary><b>PIB y cuentas nacionales</b> (3 capturas)</summary>

<p align="center"><img src="assets/screenshots/macro_pib_y_demanda_global.webp" alt="PIB y cuentas nacionales: PIB y demanda global, estructura por sector y cuadros a precios constantes y corrientes" width="100%"><br><sub>PIB y cuentas nacionales: PIB y demanda global, estructura por sector y cuadros a precios constantes y corrientes</sub></p>

<p align="center"><img src="assets/screenshots/macro_pib_capacidad_instalada_uci.webp" alt="PIB y cuentas nacionales: utilización de la capacidad instalada en la industria (UCI)" width="100%"><br><sub>PIB y cuentas nacionales: utilización de la capacidad instalada en la industria (UCI)</sub></p>

<p align="center"><img src="assets/screenshots/macro_pib_generacion_del_ingreso_cgi.webp" alt="PIB y cuentas nacionales: generación del ingreso e insumo de mano de obra (CGI)" width="100%"><br><sub>PIB y cuentas nacionales: generación del ingreso e insumo de mano de obra (CGI)</sub></p>

</details>

<details>
<summary><b>Empleo y sociedad</b> (5 capturas)</summary>

<p align="center"><img src="assets/screenshots/macro_empleo_eph_mercado_de_trabajo.webp" alt="Empleo y sociedad: mercado de trabajo (EPH), tasas principales del informe" width="100%"><br><sub>Empleo y sociedad: mercado de trabajo (EPH), tasas principales del informe</sub></p>

<p align="center"><img src="assets/screenshots/macro_empleo_salarios.webp" alt="Empleo y sociedad: índice de salarios" width="100%"><br><sub>Empleo y sociedad: índice de salarios</sub></p>

<p align="center"><img src="assets/screenshots/macro_empleo_canasta_basica.webp" alt="Empleo y sociedad: canasta básica alimentaria y total, y línea de pobreza" width="100%"><br><sub>Empleo y sociedad: canasta básica alimentaria y total, y línea de pobreza</sub></p>

<p align="center"><img src="assets/screenshots/macro_empleo_pobreza.webp" alt="Empleo y sociedad: incidencia de la pobreza y la indigencia" width="100%"><br><sub>Empleo y sociedad: incidencia de la pobreza y la indigencia</sub></p>

<p align="center"><img src="assets/screenshots/macro_empleo_distribucion_del_ingreso.webp" alt="Empleo y sociedad: distribución del ingreso (EPH) y coeficiente de Gini" width="100%"><br><sub>Empleo y sociedad: distribución del ingreso (EPH) y coeficiente de Gini</sub></p>

</details>

<details>
<summary><b>Precios mayoristas y costo de la construcción</b> (2 capturas)</summary>

<p align="center"><img src="assets/screenshots/macro_precios_mayoristas_ipim.webp" alt="Precios: precios mayoristas (SIPM) e índice de precios internos básicos" width="100%"><br><sub>Precios: precios mayoristas (SIPM) e índice de precios internos básicos</sub></p>

<p align="center"><img src="assets/screenshots/macro_precios_costos_de_construccion_icc.webp" alt="Precios: índice del costo de la construcción (ICC)" width="100%"><br><sub>Precios: índice del costo de la construcción (ICC)</sub></p>

</details>

<details>
<summary><b>Sector externo</b> (3 capturas)</summary>

<p align="center"><img src="assets/screenshots/macro_externo_intercambio_comercial_ica.webp" alt="Sector externo: intercambio comercial argentino (exportaciones, importaciones y saldo)" width="100%"><br><sub>Sector externo: intercambio comercial argentino (exportaciones, importaciones y saldo)</sub></p>

<p align="center"><img src="assets/screenshots/macro_externo_balanza_de_pagos_y_deuda.webp" alt="Sector externo: balanza de pagos y deuda externa" width="100%"><br><sub>Sector externo: balanza de pagos y deuda externa</sub></p>

<p align="center"><img src="assets/screenshots/macro_externo_turismo_internacional.webp" alt="Sector externo: turismo internacional (saldo, receptivo y emisivo)" width="100%"><br><sub>Sector externo: turismo internacional (saldo, receptivo y emisivo)</sub></p>

</details>

<details>
<summary><b>Expectativas (REM) y mercados</b> (3 capturas)</summary>

<p align="center"><img src="assets/screenshots/macro_expectativas_rem.webp" alt="Expectativas: encuesta REM del BCRA (inflación, tipo de cambio, tasa y actividad esperados)" width="100%"><br><sub>Expectativas: encuesta REM del BCRA (inflación, tipo de cambio, tasa y actividad esperados)</sub></p>

<p align="center"><img src="assets/screenshots/macro_expectativas_track_record_rem.webp" alt="Expectativas: track record del REM, lo que se esperaba contra lo que pasó" width="100%"><br><sub>Expectativas: track record del REM, lo que se esperaba contra lo que pasó</sub></p>

<p align="center"><img src="assets/screenshots/macro_mercados_merval_y_commodities.webp" alt="Mercados: S&P Merval en pesos y commodities agro" width="100%"><br><sub>Mercados: S&P Merval en pesos y commodities agro</sub></p>

</details>

### Tasa Fija y CER — curvas de rendimiento y tablas de pricing
Curvas de rendimiento sobre precios spot para renta fija a tasa fija y ajustada por CER, con la tabla de pricing completa (TIR, TEA, TEM, Duration, Convexidad) detrás de cada punto.

La curva es **logarítmica** (`TIR = a + b·ln(duration)`, dos parámetros) y se reajusta cada vez que se actualizan los precios. Se eligió por backtest y no por costumbre (detalle en «Hallazgos de análisis»): una curva más flexible pasa más cerca de cada bono pero absorbe justo el desvío que se quiere medir. Cada panel muestra el modelo, la cantidad de puntos, el R² y el error fuera de muestra; con menos de 5 bonos no dibuja curva ni calcula el «vs. Curva», y si la duration explica poco (R² < 0,25) lo avisa en el gráfico para no leer ese desvío como caro o barato. Dollar Linked conserva Nelson-Siegel.

Las tablas de pricing tienen unas 26 columnas, por eso cada una se muestra en 3 o 4 pantallas con el activo fijo a la izquierda: primero el precio neto, la TEA/TIR, TNA, TEM y TEO, la duration, la convexidad, la paridad y la sensibilidad ∆P (±100 pb); después el z-spread contra la curva libre de riesgo, el desvío contra la curva propia, el vencimiento, el volumen y las calificaciones de riesgo; y al final el valor técnico, los breakeven de dólar y el costo de operar (clase de arancel, costo de ida y vuelta y días para recuperar la comisión).

<p align="center"><img src="assets/screenshots/rf_tasa_fija_kpis_curva_logaritmica.webp" alt="Tasa Fija: KPIs del universo y curvas por clase de emisor con curva logarítmica y su calidad de ajuste" width="100%"><br><sub>Tasa Fija: KPIs del universo y curvas por clase de emisor con curva logarítmica y su calidad de ajuste</sub></p>

<p align="center"><img src="assets/screenshots/rf_tasa_fija_tabla_pricing_parte1.webp" alt="Tabla de pricing de Tasa Fija: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Tasa Fija: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_tasa_fija_tabla_pricing_parte2.webp" alt="Tabla de pricing de Tasa Fija: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Tasa Fija: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<details>
<summary>📷 Ver las 5 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/rf_tasa_fija_tabla_pricing_parte3.webp" alt="Tabla de pricing de Tasa Fija: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Tasa Fija: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_cer_kpis_curvas_logaritmicas.webp" alt="Curva CER: KPIs del universo y curvas por clase de emisor con curva logarítmica y su calidad de ajuste" width="100%"><br><sub>Curva CER: KPIs del universo y curvas por clase de emisor con curva logarítmica y su calidad de ajuste</sub></p>

<p align="center"><img src="assets/screenshots/rf_cer_tabla_pricing_parte1.webp" alt="Tabla de pricing de CER: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de CER: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_cer_tabla_pricing_parte2.webp" alt="Tabla de pricing de CER: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de CER: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_cer_tabla_pricing_parte3.webp" alt="Tabla de pricing de CER: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de CER: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

</details>

### Dollar Linked — bandas cambiarias y proyección por escenario
Evolución del A3500 contra el régimen oficial de bandas cambiarias, con brecha promedio (6M e histórica) contra el límite superior e inferior; dos escenarios de proyección — devaluación implícita en los futuros A3500 y en los futuros ROFEX — para comparar contra la curva de tasa fija. La banda cambiaria en sí se proyecta con una jerarquía de 3 fuentes (ver [Otros motores destacados](#otros-motores-destacados)): dato oficial del BCRA mientras exista, inflación real ya publicada con el rezago T-2 que el propio régimen usa, y expectativa REM recién cuando ninguna de las dos anteriores todavía cubre esa fecha — nunca una proyección lineal ingenua. La tabla de pricing se muestra para los cuatro escenarios de dólar (A3500, ROFEX, REM y banda alta); cada escenario cambia la TEA, la TNA y la duration de cada bono.

> **La banda alta es un escenario de estrés, no un pronóstico.** Supone que en cada fecha de pago el dólar vale el **techo de la banda cambiaria oficial**, en lugar de lo que descuentan los futuros (ROFEX), la encuesta REM o el A3500 de hoy. Sirve para medir cuánto rinde un Dollar Linked si la devaluación llega al límite del régimen, y por eso muestra TEA muy altas en los bonos cortos (una devaluación hasta el techo comprimida en pocas semanas) y deja sin dato a los bonos que vencen más allá del horizonte con banda proyectada. Para decidir entre Tasa Fija y Dollar Linked se compara contra ROFEX y REM, que son los escenarios de mercado y de expectativas.

<p align="center"><img src="assets/screenshots/rf_dollar_linked_bandas_cambiarias.webp" alt="Dollar Linked: evolución del A3500 contra el régimen oficial de bandas cambiarias" width="100%"><br><sub>Dollar Linked: evolución del A3500 contra el régimen oficial de bandas cambiarias</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_escenario_a3500.webp" alt="Dollar Linked: curva con el escenario de dólar A3500" width="100%"><br><sub>Dollar Linked: curva con el escenario de dólar A3500</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_escenario_rofex.webp" alt="Dollar Linked: curva con el escenario de futuros ROFEX" width="100%"><br><sub>Dollar Linked: curva con el escenario de futuros ROFEX</sub></p>

<details>
<summary>📷 Ver las 14 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_escenario_rem.webp" alt="Dollar Linked: curva con el escenario REM" width="100%"><br><sub>Dollar Linked: curva con el escenario REM</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_escenario_banda_alta.webp" alt="Dollar Linked: curva con el escenario de estrés de banda alta (dólar en el techo de la banda)" width="100%"><br><sub>Dollar Linked: curva con el escenario de estrés de banda alta (dólar en el techo de la banda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_a3500_parte1.webp" alt="Tabla de pricing de Dollar Linked, escenario de dólar A3500: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de dólar A3500: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_a3500_parte2.webp" alt="Tabla de pricing de Dollar Linked, escenario de dólar A3500: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de dólar A3500: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_a3500_parte3.webp" alt="Tabla de pricing de Dollar Linked, escenario de dólar A3500: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de dólar A3500: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_rofex_parte1.webp" alt="Tabla de pricing de Dollar Linked, escenario de futuros ROFEX: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de futuros ROFEX: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_rofex_parte2.webp" alt="Tabla de pricing de Dollar Linked, escenario de futuros ROFEX: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de futuros ROFEX: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_rofex_parte3.webp" alt="Tabla de pricing de Dollar Linked, escenario de futuros ROFEX: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de futuros ROFEX: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_rem_parte1.webp" alt="Tabla de pricing de Dollar Linked, escenario REM: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario REM: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_rem_parte2.webp" alt="Tabla de pricing de Dollar Linked, escenario REM: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario REM: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_rem_parte3.webp" alt="Tabla de pricing de Dollar Linked, escenario REM: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario REM: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_banda_alta_parte1.webp" alt="Tabla de pricing de Dollar Linked, escenario de estrés de banda alta (dólar en el techo de la banda): columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de estrés de banda alta (dólar en el techo de la banda): columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_banda_alta_parte2.webp" alt="Tabla de pricing de Dollar Linked, escenario de estrés de banda alta (dólar en el techo de la banda): columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de estrés de banda alta (dólar en el techo de la banda): columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_dollar_linked_tabla_pricing_banda_alta_parte3.webp" alt="Tabla de pricing de Dollar Linked, escenario de estrés de banda alta (dólar en el techo de la banda): columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Dollar Linked, escenario de estrés de banda alta (dólar en el techo de la banda): columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

</details>

### Soberanos y Provinciales USD — MEP implícito y arbitraje legislativo
Ratios de dólar MEP implícito por activo, spread en bps entre pares de bonos soberanos bajo ley local vs. extranjera (AL29/GD29, AL30/GD30, etc.), y curvas por ley (local, extranjera, deuda provincial y Bopreales).

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_kpis_riesgo_pais.webp" alt="Soberanos y provinciales USD: KPIs, curvas y riesgo país contra la región" width="100%"><br><sub>Soberanos y provinciales USD: KPIs, curvas y riesgo país contra la región</sub></p>

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_mep_implicito_arbitraje_legislativo.webp" alt="Soberanos USD: MEP implícito y spreads de legislación local vs. Nueva York" width="100%"><br><sub>Soberanos USD: MEP implícito y spreads de legislación local vs. Nueva York</sub></p>

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_tabla_pricing_parte1.webp" alt="Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 1 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 1 de 4 (Activo fijo a la izquierda)</sub></p>

<details>
<summary>📷 Ver las 4 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_tabla_pricing_parte2.webp" alt="Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 2 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 2 de 4 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_tabla_pricing_parte3.webp" alt="Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 3 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 3 de 4 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_tabla_pricing_parte4.webp" alt="Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 4 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de soberanos y provinciales en USD: columnas de la pantalla 4 de 4 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_soberanos_usd_curvas_por_clase.webp" alt="Soberanos y provinciales USD: curvas por clase de emisor" width="100%"><br><sub>Soberanos y provinciales USD: curvas por clase de emisor</sub></p>

</details>

### Corporativos USD — 189 Obligaciones Negociables por ley y por sector
Universo completo de ONs corporativas, segmentadas por ley de emisión (local/extranjera) y por sector económico (agrupa emisores como YPF/PAE/Vista bajo "Oil & Gas", exige un mínimo de 3 tickers para que un sector tenga curva propia).

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_curvas_por_ley.webp" alt="Corporativos USD: 189 Obligaciones Negociables, curvas por ley de emisión" width="100%"><br><sub>Corporativos USD: 189 Obligaciones Negociables, curvas por ley de emisión</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_curvas_por_sector_emisor.webp" alt="Corporativos USD: curvas por sector y emisor" width="100%"><br><sub>Corporativos USD: curvas por sector y emisor</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_tabla_pricing_parte1.webp" alt="Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 1 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 1 de 4 (Activo fijo a la izquierda)</sub></p>

<details>
<summary>📷 Ver las 8 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_tabla_pricing_parte2.webp" alt="Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 2 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 2 de 4 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_tabla_pricing_parte3.webp" alt="Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 3 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 3 de 4 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_tabla_pricing_parte4.webp" alt="Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 4 de 4 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de Obligaciones Negociables en USD: columnas de la pantalla 4 de 4 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_usd_relative_value.webp" alt="Corporativos USD: oportunidades de valor relativo" width="100%"><br><sub>Corporativos USD: oportunidades de valor relativo</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_ars_curvas_por_tipo_emisor.webp" alt="Corporativos y provinciales en pesos: curvas por tipo de emisor y por sector" width="100%"><br><sub>Corporativos y provinciales en pesos: curvas por tipo de emisor y por sector</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_ars_tabla_pricing_parte1.webp" alt="Tabla de pricing de corporativos y provinciales en pesos: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de corporativos y provinciales en pesos: columnas de la pantalla 1 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_ars_tabla_pricing_parte2.webp" alt="Tabla de pricing de corporativos y provinciales en pesos: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de corporativos y provinciales en pesos: columnas de la pantalla 2 de 3 (Activo fijo a la izquierda)</sub></p>

<p align="center"><img src="assets/screenshots/rf_corporativos_ars_tabla_pricing_parte3.webp" alt="Tabla de pricing de corporativos y provinciales en pesos: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)" width="100%"><br><sub>Tabla de pricing de corporativos y provinciales en pesos: columnas de la pantalla 3 de 3 (Activo fijo a la izquierda)</sub></p>

</details>

### Ficha de bono: drill-down completo por instrumento
Click en cualquier ticker de las tablas de pricing de arriba abre una vista de detalle dedicada
para ese instrumento: métricas de sensibilidad completas (TIR, Paridad, Duration, **Vida
Promedio** —distinta de Duration: pesa solo la devolución de capital sin descontar, el número
que de verdad importa en bonos que amortizan en cuotas, no al vencimiento entero—, Convexidad,
DV01 en dos escalas —por 100 de nominal y por una posición real de VN 1.000.000—, spread contra
la curva propia del emisor y concentración del emisor en el universo), detalle completo del
prospecto, cronograma de flujos con Valor Presente de cada flujo (misma estructura que un
modelo de manual de finanzas), gráfico de sensibilidad Precio-vs-TIR con la aproximación
clásica de Duration-Convexidad superpuesta contra el reprecio exacto, un **mini-Breakeven a
+3/+6/+12 meses** (misma lógica que la Matriz de Breakeven completa, ver [Capturas](#capturas),
sin tener que salir de la ficha para una primera lectura del margen de seguridad), comparables
por duration dentro de la misma clase de emisor, otros instrumentos del mismo emisor exacto, y
un botón para exportar la misma ficha, completa, a Excel.

<p align="center"><img src="assets/screenshots/ficha_hard_dollar_AL30_kpis_riesgo_vistas.webp" alt="Ficha AL30: KPIs, riesgo crediticio y vistas de precio Mercado / Compra / Venta" width="100%"><br><sub>Ficha AL30: KPIs, riesgo crediticio y vistas de precio Mercado / Compra / Venta</sub></p>

<p align="center"><img src="assets/screenshots/ficha_hard_dollar_AL30_prospecto_cronograma.webp" alt="Ficha AL30: detalle del prospecto y cronograma de flujos con PVCF y fila de totales" width="100%"><br><sub>Ficha AL30: detalle del prospecto y cronograma de flujos con PVCF y fila de totales</sub></p>

<p align="center"><img src="assets/screenshots/ficha_hard_dollar_AL30_breakeven_comparables.webp" alt="Ficha AL30: mini-breakeven a 3, 6 y 12 meses y comparables" width="100%"><br><sub>Ficha AL30: mini-breakeven a 3, 6 y 12 meses y comparables</sub></p>

<details>
<summary>📷 Ver las 11 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/ficha_cer_TX28_kpis_riesgo_vistas.webp" alt="Ficha CER (TX28): TIR real, coeficiente CER y vistas de precio" width="100%"><br><sub>Ficha CER (TX28): TIR real, coeficiente CER y vistas de precio</sub></p>

<p align="center"><img src="assets/screenshots/ficha_cer_TX28_prospecto_cronograma.webp" alt="Ficha CER (TX28): cronograma en pesos nominales con coeficiente CER proyectado" width="100%"><br><sub>Ficha CER (TX28): cronograma en pesos nominales con coeficiente CER proyectado</sub></p>

<p align="center"><img src="assets/screenshots/ficha_cer_TX28_breakeven_comparables.webp" alt="Ficha CER (TX28): mini-breakeven y comparables" width="100%"><br><sub>Ficha CER (TX28): mini-breakeven y comparables</sub></p>

<p align="center"><img src="assets/screenshots/ficha_tasa_fija_S30O6_kpis_riesgo_vistas.webp" alt="Ficha Tasa Fija (S30O6): TEA, TNA y vistas de precio" width="100%"><br><sub>Ficha Tasa Fija (S30O6): TEA, TNA y vistas de precio</sub></p>

<p align="center"><img src="assets/screenshots/ficha_tasa_fija_S30O6_prospecto_cronograma.webp" alt="Ficha Tasa Fija (S30O6): prospecto y cronograma" width="100%"><br><sub>Ficha Tasa Fija (S30O6): prospecto y cronograma</sub></p>

<p align="center"><img src="assets/screenshots/ficha_tasa_fija_S30O6_breakeven_comparables.webp" alt="Ficha Tasa Fija (S30O6): mini-breakeven y comparables" width="100%"><br><sub>Ficha Tasa Fija (S30O6): mini-breakeven y comparables</sub></p>

<p align="center"><img src="assets/screenshots/ficha_dollar_linked_TZV27_kpis_vistas.webp" alt="Ficha Dollar Linked (TZV27): escenario de dólar, KPIs y vistas de precio" width="100%"><br><sub>Ficha Dollar Linked (TZV27): escenario de dólar, KPIs y vistas de precio</sub></p>

<p align="center"><img src="assets/screenshots/ficha_dollar_linked_TZV27_cronograma.webp" alt="Ficha Dollar Linked (TZV27): cronograma en USD y ARS" width="100%"><br><sub>Ficha Dollar Linked (TZV27): cronograma en USD y ARS</sub></p>

<p align="center"><img src="assets/screenshots/ficha_corporativo_ars_RVS1O_kpis_riesgo_vistas.webp" alt="Ficha corporativo en pesos (RVS1O): KPIs y vistas de precio" width="100%"><br><sub>Ficha corporativo en pesos (RVS1O): KPIs y vistas de precio</sub></p>

<p align="center"><img src="assets/screenshots/ficha_corporativo_ars_RVS1O_prospecto_cronograma.webp" alt="Ficha corporativo en pesos (RVS1O): prospecto y cronograma" width="100%"><br><sub>Ficha corporativo en pesos (RVS1O): prospecto y cronograma</sub></p>

<p align="center"><img src="assets/screenshots/ficha_corporativo_ars_RVS1O_breakeven_comparables.webp" alt="Ficha corporativo en pesos (RVS1O): mini-breakeven y comparables" width="100%"><br><sub>Ficha corporativo en pesos (RVS1O): mini-breakeven y comparables</sub></p>

</details>

### Tablero de Arbitraje: señales cuantitativas de trading
Dos motores de detección de desfasajes de precio, con acción sugerida (comprar/vender) calculada en vivo:

- **Inflación implícita vs. mercado (BEI vs. REM):** empareja una Lecap y un bono CER que vencen el mismo día para extraer la inflación que el mercado está pricing (BEI) y la compara contra el consenso de analistas (REM). Si el bono CER rinde tan poco que asume una inflación irrealmente alta, señala vender ese CER y pasarse a la Lecap (LONG FIJA).
- **Arbitraje FX sintético (Dollar Linked vs. Tasa Fija):** simula el rendimiento de un bono Dollar Linked asumiendo que el dólar devalúa según lo que pricean los futuros de dólar (obtenidos por scraping), y lo compara contra una Lecap del mismo plazo — si la devaluación implícita en el Dollar Linked es menor a la que rendiría la tasa fija, señala vender el Dollar Linked y pasarse a tasa fija.

<p align="center"><img src="assets/screenshots/rf_tablero_arbitraje_bei_fija_vs_cer.webp" alt="Tablero de arbitraje: breakeven de inflación (BEI) de Tasa Fija vs. CER contra el REM" width="100%"><br><sub>Tablero de arbitraje: breakeven de inflación (BEI) de Tasa Fija vs. CER contra el REM</sub></p>

<p align="center"><img src="assets/screenshots/rf_tablero_arbitraje_historia_breakeven.webp" alt="Tablero de arbitraje: historia del breakeven de inflación y quién acertó, el breakeven o el REM" width="100%"><br><sub>Tablero de arbitraje: historia del breakeven de inflación y quién acertó, el breakeven o el REM</sub></p>

<p align="center"><img src="assets/screenshots/rf_tablero_arbitraje_fx_sintetico.webp" alt="Tablero de arbitraje: arbitraje FX sintético (Dollar Linked vs. Tasa Fija)" width="100%"><br><sub>Tablero de arbitraje: arbitraje FX sintético (Dollar Linked vs. Tasa Fija)</sub></p>

### Carry Trade — breakeven de devaluación vs. expectativas de mercado
Para cada bono a tasa fija en pesos (LECAP/BONCAP/BONO TF), calcula el tipo de cambio *breakeven* — al que el mercado tendría que llegar en el vencimiento para que un carry trade (vender USD, comprar el bono en pesos, recomprar USD al cobrar) empate exactamente contra haberse quedado en dólares — y lo compara contra tres expectativas de mercado: REM (encuesta de expectativas del BCRA), futuros ROFEX y las bandas cambiarias oficiales, todas anualizadas por composición (TEA, no TNA — una auditoría propia de convención de tasas encontró y corrigió esa etiqueta) para poder comparar bonos de distinto plazo en igualdad de condiciones. Señala si el carry gana, pierde o queda neutro contra cada expectativa, y suma una comparación directa en pesos (sin pasar por el tipo de cambio) contra un bono Dollar Linked de duration similar.

Las 3 expectativas de mercado no siempre cubren el mismo horizonte (el REM releva solo ~4-5 meses hacia adelante, los futuros ROFEX bastante más) — en vez de estirar la más corta con el último valor conocido como si fuera una proyección real, la curva y la tabla se acotan juntas a la última fecha donde las tres siguen teniendo dato genuino, y un bono que vence más allá de ese punto se excluye del análisis en lugar de mostrar una señal fabricada contra una expectativa que no existe. La línea de banda cambiaria, además, cambia de color en el punto exacto donde deja de ser cronograma oficial del BCRA y pasa a ser proyección con inflación esperada del REM — la incertidumbre queda visible, no escondida detrás de una línea continua.

<p align="center"><img src="assets/screenshots/rf_carry_trade_curva_de_carry.webp" alt="Carry Trade: breakeven de devaluación de cada bono contra REM, ROFEX y bandas, anualizado por TEA" width="100%"><br><sub>Carry Trade: breakeven de devaluación de cada bono contra REM, ROFEX y bandas, anualizado por TEA</sub></p>

<p align="center"><img src="assets/screenshots/rf_carry_trade_yield_usd_sintetico.webp" alt="Carry Trade: yield en USD sintético (paridad cubierta de tasas) por instrumento" width="100%"><br><sub>Carry Trade: yield en USD sintético (paridad cubierta de tasas) por instrumento</sub></p>

<p align="center"><img src="assets/screenshots/rf_carry_trade_operacion_completa.webp" alt="Carry Trade: operación completa de cuatro patas con comisiones y resultado en dólares por bono y escenario" width="100%"><br><sub>Carry Trade: operación completa de cuatro patas con comisiones y resultado en dólares por bono y escenario</sub></p>

<p align="center"><img src="assets/screenshots/rf_carry_trade_simulador_interactivo.webp" alt="Carry Trade: simulador interactivo" width="100%"><br><sub>Carry Trade: simulador interactivo</sub></p>

### Qué cambió hoy y Tasa Fija con forwards y roll-down
Resumen de la rueda: cambio de TIR de cada clase contra la rueda anterior, cambio de la curva de letras y alertas de curva con umbrales editables. En Tasa Fija, forwards implícitas a 3 y 6 meses (solo donde la curva llega; nunca se extrapola), roll-down, colchón de tasa y **días que tarda el carry en pagar la comisión**.

<p align="center"><img src="assets/screenshots/rf_que_cambio_hoy.webp" alt="Qué cambió hoy: curva de letras en pesos contra la rueda anterior y alertas de curva" width="100%"><br><sub>Qué cambió hoy: curva de letras en pesos contra la rueda anterior y alertas de curva</sub></p>

<p align="center"><img src="assets/screenshots/rf_que_cambio_hoy_movimiento_tir.webp" alt="Qué cambió hoy: movimiento de la TIR por clase de activo" width="100%"><br><sub>Qué cambió hoy: movimiento de la TIR por clase de activo</sub></p>

<p align="center"><img src="assets/screenshots/rf_tasa_fija_curva_y_forwards.webp" alt="Tasa Fija: curva y forwards implícitas a 3 y 6 meses" width="100%"><br><sub>Tasa Fija: curva y forwards implícitas a 3 y 6 meses</sub></p>

<p align="center"><img src="assets/screenshots/rf_tasa_fija_roll_down_y_colchon.webp" alt="Tasa Fija: roll-down y colchón de tasa" width="100%"><br><sub>Tasa Fija: roll-down y colchón de tasa</sub></p>

### Valor Relativo — spreads de legislación con estudio de eventos
AL/GD por par (AL29/GD29, AL30/GD30, AL35/GD35, AE38/GD38, AL41/GD41): z-score contra su propia historia, vida media de reversión, estadístico tipo Dickey-Fuller y **edge neto de comisiones**; estudio de eventos calculado solo con datos anteriores a cada señal; y caro/barato de cada bono contra la curva Nelson-Siegel soberana. Distingue "cubre el costo de rotar", "desvío menor al costo" y "no revierte", para no confundir un cambio de nivel con una oportunidad.

<p align="center"><img src="assets/screenshots/rf_valor_relativo_spreads_de_legislacion.webp" alt="Valor relativo: spreads de legislación AL/GD con z-score, vida media y edge neto" width="100%"><br><sub>Valor relativo: spreads de legislación AL/GD con z-score, vida media y edge neto</sub></p>

<p align="center"><img src="assets/screenshots/rf_valor_relativo_explorador_de_pares.webp" alt="Valor relativo: explorador de cualquier par de bonos" width="100%"><br><sub>Valor relativo: explorador de cualquier par de bonos</sub></p>

<p align="center"><img src="assets/screenshots/rf_valor_relativo_caro_barato_curva_soberana.webp" alt="Valor relativo: cada bono contra la curva soberana Nelson-Siegel" width="100%"><br><sub>Valor relativo: cada bono contra la curva soberana Nelson-Siegel</sub></p>

<p align="center"><img src="assets/screenshots/rf_valor_relativo_spread_crediticio_sector.webp" alt="Valor relativo: spread crediticio por sector" width="100%"><br><sub>Valor relativo: spread crediticio por sector</sub></p>

### UST vs Soberanos, MEP/Brecha y Carry Trade completo
¿Los soberanos siguen al Tesoro? Correlación, beta y R² contra el Tesoro de igual duration (resultado: explica ~1% de la variación semanal de la TIR). MEP y CCL implícitos por bono con la brecha contra el A3500 y el costo real de convertir. El carry trade como **operación completa de cuatro patas** (USD → bono → pesos → activo → pesos → bono → USD) con comisiones en cada pata y varios escenarios de dólar de salida.

<p align="center"><img src="assets/screenshots/rf_ust_vs_soberanos_niveles_y_correlacion.webp" alt="UST vs Soberanos: niveles, correlación, beta y R² contra el Tesoro de igual duration" width="100%"><br><sub>UST vs Soberanos: niveles, correlación, beta y R² contra el Tesoro de igual duration</sub></p>

<p align="center"><img src="assets/screenshots/rf_ust_vs_soberanos_mapa_bono_por_tenor.webp" alt="UST vs Soberanos: correlación de cada bono con cada tenor del Tesoro" width="100%"><br><sub>UST vs Soberanos: correlación de cada bono con cada tenor del Tesoro</sub></p>

<p align="center"><img src="assets/screenshots/rf_mep_brecha_en_vivo.webp" alt="MEP / Brecha: MEP y CCL en vivo, otros dólares, conversión por bono e intradía" width="100%"><br><sub>MEP / Brecha: MEP y CCL en vivo, otros dólares, conversión por bono e intradía</sub></p>

<p align="center"><img src="assets/screenshots/rf_mep_brecha_historica.webp" alt="MEP / Brecha: brecha histórica diaria contra el A3500" width="100%"><br><sub>MEP / Brecha: brecha histórica diaria contra el A3500</sub></p>

### Armador de Portafolios, Radar y Simulador de Posición
Armador de portafolios hard dollar (duration objetivo, TIR objetivo o Markowitz) donde el monto es el **desembolso total con comisiones**, con costo de implementación y TIR de ida y vuelta; Radar de oportunidades; y un simulador "¿y si compro o vendo X?" con el resultado neto de ida y vuelta.

<p align="center"><img src="assets/screenshots/rf_radar_de_oportunidades.webp" alt="Radar de oportunidades: alertas de riesgo, mayor margen de seguridad y desarbitrajes activos" width="100%"><br><sub>Radar de oportunidades: alertas de riesgo, mayor margen de seguridad y desarbitrajes activos</sub></p>

<p align="center"><img src="assets/screenshots/rf_simulador_de_posicion.webp" alt="Simulador de posición: qué pasa si compro o vendo un bono, con el resultado neto de ida y vuelta" width="100%"><br><sub>Simulador de posición: qué pasa si compro o vendo un bono, con el resultado neto de ida y vuelta</sub></p>

### Armador con optimización de Markowitz — cartera de 9 bonos con topes por emisor y por sector
El Armador construye la cartera de **máximo Sharpe** (el óptimo de Markowitz en el sentido habitual; la alternativa es mínima varianza pura) sobre el universo de soberanos y corporativos en dólares, con la covarianza estimada de los retornos diarios en dólares de la historia de precios. Esta corrida usa los filtros de un inversor real:

- **Liquidez**: volumen mínimo de **100.000 nominales operados hoy** (69 bonos elegibles ese día; 188 quedan afuera por volumen).
- **Capital**: **USD 10.000** (el monto es el desembolso **total, comisiones incluidas**: precio neto de compra con comisión e IVA, cupones netos de la comisión de renta).
- **Concentración**: de **6 a 10 bonos**, **tope del 25 % por emisor** y **30 % por sector**.

El resultado (9 bonos) cumple los topes: el mayor emisor pesa 25.00 % y el mayor sector (Oil & Gas) 30.00 %. Comisión e IVA de la compra: USD 63.88.

| Ticker | Emisor | Sector | Peso | Monto (USD) | Nominal (VN) | Precio neto | TIR | Duration | Volumen hoy |
|---|---|---|---:|---:|---:|---:|---:|---:|---:|
| **DNCBO** | Empresa Distribuidora y Comercializadora Norte S.A. (EDENOR) | Energía (Distribución Eléctrica) | 25.00% | $2,500.00 | $2,553.92 | $97.89 | 9.47% | 2.2405 | 250,844 |
| **CO35D** | Provincia de Cordoba | Provincial | 16.02% | $1,601.96 | $1,589.94 | $100.76 | 8.92% | 5.0238 | 163,000 |
| **YM43O** | YPF S.A. | Oil & Gas | 14.26% | $1,426.32 | $1,417.75 | $100.61 | 6.26% | 2.9586 | 402,132 |
| **MCC3O** | Pecom Servicios Energía S.A.U. | Oil & Gas | 13.59% | $1,358.64 | $1,323.99 | $102.62 | 7.81% | 2.8764 | 294,109 |
| **TSC4O** | Transportadora de Gas del Sur S.A. (TGS) | Energía (Transporte de Gas Natural) | 12.59% | $1,258.72 | $1,182.01 | $106.49 | 7.33% | 6.1075 | 247,000 |
| **MGCRO** | Pampa Energia S.A. | Energia | 5.86% | $586.03 | $564.44 | $103.82 | 7.78% | 6.8876 | 1,985,000 |
| **AN29** | Estado Nacional | Soberano | 5.81% | $580.57 | $650.45 | $89.26 | 11.96% | 2.4865 | 369,293 |
| **AO28** | Estado Nacional | Soberano | 4.73% | $472.72 | $502.06 | $94.16 | 9.70% | 1.7511 | 3,298,621 |
| **VSCXO** | Vista Energy Argentina S.A.U. | Oil & Gas | 2.15% | $215.04 | $206.82 | $103.98 | 7.44% | 6.8768 | 1,010,000 |

Debajo de la composición, el Armador muestra las métricas de la cartera como un flujo agregado (TIR y duration «rigurosas» contra el promedio ponderado simple), el costo de implementación y la TIR de ida y vuelta a 12 meses, el calendario de flujos proyectado por bono, **por qué esta combinación**, el estrés de tasa con re-descuento completo, la correlación entre los bonos elegidos, la **frontera eficiente**, el backtest retroactivo y las métricas de riesgo (VaR, CVaR, Sortino) calculadas sobre esa serie. **El sistema muestra también sus límites**: esas métricas cubren 7 de los 9 bonos (DNCBO y MCC3O no alcanzan el 50 % de cobertura de precios de los últimos 12 meses y quedan afuera, con los pesos renormalizados) y el Sortino del backtest retroactivo sale negativo (−2,23); el aviso está en pantalla, no escondido.

<p align="center"><img src="assets/screenshots/armador_markowitz_1_configuracion.webp" alt="Configuración: máximo Sharpe, volumen mínimo 100.000, USD 10.000, 6 a 10 bonos, tope 25 % por emisor y 30 % por sector" width="100%"><br><sub>Configuración: máximo Sharpe, volumen mínimo 100.000, USD 10.000, 6 a 10 bonos, tope 25 % por emisor y 30 % por sector</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_2_composicion_sugerida.webp" alt="Composición sugerida con peso, monto, nominales, precio neto de compra, TIR y duration" width="100%"><br><sub>Composición sugerida con peso, monto, nominales, precio neto de compra, TIR y duration</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_3_metricas_de_la_cartera.webp" alt="Métricas de la cartera: TIR y duration rigurosas, costo de implementación y TIR de ida y vuelta" width="100%"><br><sub>Métricas de la cartera: TIR y duration rigurosas, costo de implementación y TIR de ida y vuelta</sub></p>

<details>
<summary>📷 Ver las 7 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/armador_markowitz_4_calendario_de_flujos.webp" alt="Calendario de flujos proyectado por bono y recupero acumulado" width="100%"><br><sub>Calendario de flujos proyectado por bono y recupero acumulado</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_5_por_que_esta_combinacion.webp" alt="Por qué esta combinación: objetivo, desvío, VaR y concentración" width="100%"><br><sub>Por qué esta combinación: objetivo, desvío, VaR y concentración</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_6_estres_de_tasa.webp" alt="Estrés de tasa con re-descuento completo" width="100%"><br><sub>Estrés de tasa con re-descuento completo</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_7_correlacion.webp" alt="Correlación entre los bonos sugeridos" width="100%"><br><sub>Correlación entre los bonos sugeridos</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_8_frontera_eficiente.webp" alt="Frontera eficiente" width="100%"><br><sub>Frontera eficiente</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_9_backtest_historico.webp" alt="Backtest histórico retroactivo de la cartera" width="100%"><br><sub>Backtest histórico retroactivo de la cartera</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_10_metricas_de_riesgo.webp" alt="Métricas de riesgo sobre la serie del backtest (VaR, CVaR, Sortino)" width="100%"><br><sub>Métricas de riesgo sobre la serie del backtest (VaR, CVaR, Sortino)</sub></p>

</details>

### Comisiones del broker y vistas Mercado / Compra / Venta
Selector global de **vista de precios** (*Mercado* = último precio operado, sin comisión; *Compra* = precio neto de comprar, con comisión e IVA; *Venta* = lo que se cobra al vender) y panel de **comisiones del broker**: tarifas por clase de activo con su fuente y vigencia, IVA y derechos de mercado editables, y recálculo del pricing con el perfil elegido. Ver [Comisiones y vistas Mercado / Compra / Venta](#comisiones-y-vistas-mercado--compra--venta).

<p align="center"><img src="assets/screenshots/rf_sidebar_filtros_globales_vista_de_precios.webp" alt="Vista de precios Mercado / Compra / Venta en la barra lateral" width="100%"><br><sub>Vista de precios Mercado / Compra / Venta en la barra lateral</sub></p>

<p align="center"><img src="assets/screenshots/rf_panel_comisiones_del_broker.webp" alt="Panel de comisiones del broker: tarifas por clase, IVA y derechos de mercado editables" width="100%"><br><sub>Panel de comisiones del broker: tarifas por clase, IVA y derechos de mercado editables</sub></p>

### Matriz de Sensibilidad: TIR Breakeven (Total Return)
Para cada instrumento de una categoría (Hard Dollar, CER, Tasa Fija, Dollar Linked, Corporativos ARS, Duales), calcula el retorno total de comprar hoy y vender en una fecha objetivo elegida, contra una grilla de TIRs de salida hipotéticas, con 3 estrategias de reinversión de los cupones cobrados en el camino (caja al 0%, tasa de mercado dinámica, o reinversión a la TIR del propio bono) — y una columna **BREAKEVEN**: la TIR de salida máxima que el instrumento puede tolerar antes de que la posición pase a pérdida nominal, más una columna **Margen** (Breakeven − TIR de hoy, en puntos porcentuales) para leer de un vistazo qué tan ajustada está cada posición. Un resumen agregado arriba de la tabla (bonos totales, margen promedio, cuántos con margen bajo o negativo) evita tener que leer fila por fila para notar que una categoría entera quedó apretada, y una alerta equivalente aparece directo en el brief matutino para los Soberanos Hard Dollar.

Cada categoría respeta su propia unidad de tasa en vez de forzar una sola convención: para CER, la TIR de salida es **real** (post-inflación) — los flujos remanentes se descuentan en base real (VN100, sin CER) y el precio de salida se reinfla una única vez con el coeficiente CER proyectado a la fecha de salida elegida, no con el de cada cupón individual (que ya lleva la inflación de esa fecha puntual incorporada) — evitando el error clásico de contar la inflación proyectada dos veces. Para Dollar Linked se puede elegir el escenario de FX de referencia (REM/ROFEX/A3500/Banda). Para la estrategia de reinversión "tasa de mercado", el input se pide como TNA (como cotiza una caución real) y se convierte a TEA antes de capitalizar — capitalizarla directo como si ya fuera una TEA anual (un error real, encontrado y corregido en la propia auditoría del proyecto) subestima cuánto rinde una caución que se renueva cada pocos días, no una vez al año.

Los bonos Duales entran a la matriz con la pata (TAMAR/CER/Fija/Dollar Linked) que efectivamente paga más **en cada fecha de pago** — no la que gana hoy asumida fija para siempre — reusando el mismo cronograma que ya resuelve la TIR del instrumento internamente. Cada ficha de bono individual, además, muestra un mini-Breakeven directo (+3/+6/+12 meses) sin tener que ir a buscarlo a esta pestaña, y toda la matriz se puede exportar a Excel con los valores numéricos reales (no el texto ya formateado de pantalla), con escala de color condicional.

<p align="center"><img src="assets/screenshots/breakeven_1_supuestos_y_resumen.webp" alt="Matriz de sensibilidad: supuestos y resumen de riesgo agregado" width="100%"><br><sub>Matriz de sensibilidad: supuestos y resumen de riesgo agregado</sub></p>

<p align="center"><img src="assets/screenshots/breakeven_2_perfil_retorno_total_soberanos_ley_local_real.webp" alt="Perfil de retorno total en términos reales: soberanos ley local (AL29, AL30, AL35, AL41) y AO28 / AO29" width="100%"><br><sub>Perfil de retorno total en términos reales: soberanos ley local (AL29, AL30, AL35, AL41) y AO28 / AO29</sub></p>

<p align="center"><img src="assets/screenshots/breakeven_3_matriz_tir_breakeven_ordenada_por_bono.webp" alt="Matriz de TIR breakeven por bono y TIR de salida (retorno nominal y real)" width="100%"><br><sub>Matriz de TIR breakeven por bono y TIR de salida (retorno nominal y real)</sub></p>

### Semáforo de estrés sistémico global (GSSI) y tasas americanas
Índice compuesto de estrés de mercado (percentiles históricos de VIX, MOVE, VVIX, CISS), curva del Tesoro americano, TIPS, inflación breakeven, y las dos curvas que la propia Fed de Nueva York usa como modelo oficial de probabilidad de recesión (T10Y2Y y T10Y3M), con historia desde fines de los '70.

<p align="center"><img src="assets/screenshots/liquidez_gssi_semaforo_de_estres_sistemico.webp" alt="Semáforo de estrés sistémico global (GSSI): VIX, MOVE, VVIX y CISS en un índice de 0 a 100" width="100%"><br><sub>Semáforo de estrés sistémico global (GSSI): VIX, MOVE, VVIX y CISS en un índice de 0 a 100</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_curva_del_tesoro_americano.webp" alt="Curva de rendimientos del Tesoro americano por tenor (FRED)" width="100%"><br><sub>Curva de rendimientos del Tesoro americano por tenor (FRED)</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_curva_tips_e_inflacion_esperada.webp" alt="Curva TIPS (rendimiento real) e inflación esperada por el mercado (breakeven a 5 y 10 años y curva actual por plazo)" width="100%"><br><sub>Curva TIPS (rendimiento real) e inflación esperada por el mercado (breakeven a 5 y 10 años y curva actual por plazo)</sub></p>

<details>
<summary>📷 Ver las 3 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/liquidez_historia_de_la_curva_tenor_por_tenor.webp" alt="Historia de la curva del Tesoro tenor por tenor, nominal y real, desde 2001" width="100%"><br><sub>Historia de la curva del Tesoro tenor por tenor, nominal y real, desde 2001</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_fed_balance_sheet_y_net_liquidity.webp" alt="Balance de la Fed (WALCL) y liquidez neta de EE.UU. (WALCL − TGA − RRP)" width="100%"><br><sub>Balance de la Fed (WALCL) y liquidez neta de EE.UU. (WALCL − TGA − RRP)</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_contexto_internacional_bis_bce.webp" alt="Contexto internacional (BIS y BCE): tasas de política monetaria, curva AAA de la Eurozona y riesgo sistémico Europa vs EE.UU." width="100%"><br><sub>Contexto internacional (BIS y BCE): tasas de política monetaria, curva AAA de la Eurozona y riesgo sistémico Europa vs EE.UU.</sub></p>

</details>

### Mercados globales, Macro EE.UU. y economía real
Liquidez Global no es solo la Fed: tabla de *movers* del día, acciones (índices y sectores de EE.UU., índices del mundo), renta fija en ETFs, commodities, energía y forex; y la economía real de EE.UU. — inflación y tasas, mercado laboral, actividad y ciclo, probabilidad de recesión, mercado inmobiliario, salud del consumidor y producción industrial. Cada gráfico lleva su fecha de última actualización.

<p align="center"><img src="assets/screenshots/liquidez_mercados_movers_del_dia.webp" alt="Movers del día: todo el universo de mercados ordenado por variación a 1 día, 5 días, 1 mes y en el año" width="100%"><br><sub>Movers del día: todo el universo de mercados ordenado por variación a 1 día, 5 días, 1 mes y en el año</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_mercados_acciones_indices_eeuu.webp" alt="Mercados: performance relativa de los índices de acciones de EE.UU. (S&P 500, Nasdaq 100, Dow Jones, Russell 2000)" width="100%"><br><sub>Mercados: performance relativa de los índices de acciones de EE.UU. (S&P 500, Nasdaq 100, Dow Jones, Russell 2000)</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_mercados_acciones_sectores_eeuu.webp" alt="Mercados: los 11 sectores GICS de EE.UU. con su gráfico de velas y RSI" width="100%"><br><sub>Mercados: los 11 sectores GICS de EE.UU. con su gráfico de velas y RSI</sub></p>

<details>
<summary>📷 Ver las 12 capturas restantes de esta sección</summary>

<p align="center"><img src="assets/screenshots/liquidez_mercados_acciones_indices_mundo.webp" alt="Mercados: performance relativa de los índices de acciones del mundo (emergentes, Nikkei, DAX, FTSE, Hang Seng, Bovespa)" width="100%"><br><sub>Mercados: performance relativa de los índices de acciones del mundo (emergentes, Nikkei, DAX, FTSE, Hang Seng, Bovespa)</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_mercados_renta_fija_etfs.webp" alt="Mercados: ETFs de renta fija (HYG, IEF, TLT)" width="100%"><br><sub>Mercados: ETFs de renta fija (HYG, IEF, TLT)</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_mercados_commodities.webp" alt="Mercados: oro, plata, cobre, platino, petróleo, soja, maíz, trigo y café" width="100%"><br><sub>Mercados: oro, plata, cobre, platino, petróleo, soja, maíz, trigo y café</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_mercados_energia.webp" alt="Mercados: WTI, Brent y gas natural" width="100%"><br><sub>Mercados: WTI, Brent y gas natural</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_mercados_forex.webp" alt="Mercados: DXY, EUR/USD, GBP/USD, USD/JPY, USD/CNY y USD/BRL" width="100%"><br><sub>Mercados: DXY, EUR/USD, GBP/USD, USD/JPY, USD/CNY y USD/BRL</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_macro_eeuu_inflacion_y_tasas.webp" alt="Macro EE.UU.: tablero de inflación y tasas (CPI, núcleo, PCE, PPI, Fed Funds) y CPI general vs. núcleo" width="100%"><br><sub>Macro EE.UU.: tablero de inflación y tasas (CPI, núcleo, PCE, PPI, Fed Funds) y CPI general vs. núcleo</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_macro_eeuu_mercado_laboral.webp" alt="Macro EE.UU.: tasa de desempleo y nómina no agrícola mensual" width="100%"><br><sub>Macro EE.UU.: tasa de desempleo y nómina no agrícola mensual</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_macro_eeuu_actividad_y_ciclo.webp" alt="Macro EE.UU.: tasa de la Fed y precios mayoristas (PPI) como indicadores adelantados del ciclo" width="100%"><br><sub>Macro EE.UU.: tasa de la Fed y precios mayoristas (PPI) como indicadores adelantados del ciclo</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_macro_eeuu_recesion_y_nowcasting.webp" alt="Recesión y nowcasting: GDPNow de Atlanta, índice económico semanal de la Fed de Nueva York y probabilidad de recesión" width="100%"><br><sub>Recesión y nowcasting: GDPNow de Atlanta, índice económico semanal de la Fed de Nueva York y probabilidad de recesión</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_economia_real_inmobiliario.webp" alt="Mercado inmobiliario de EE.UU.: índice Case-Shiller, tasa hipotecaria nominal y real, permisos e inicios de obra" width="100%"><br><sub>Mercado inmobiliario de EE.UU.: índice Case-Shiller, tasa hipotecaria nominal y real, permisos e inicios de obra</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_economia_real_consumidor.webp" alt="Salud del consumidor de EE.UU.: crédito total y revolving (tarjetas)" width="100%"><br><sub>Salud del consumidor de EE.UU.: crédito total y revolving (tarjetas)</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_economia_real_produccion_industrial.webp" alt="Producción industrial y utilización de la capacidad instalada de EE.UU." width="100%"><br><sub>Producción industrial y utilización de la capacidad instalada de EE.UU.</sub></p>

</details>

### Charting estilo TradingView, niveles y backtest honesto de señales
Velas diarias, semanales, mensuales e intradía de 30 minutos sobre ~65 activos (y cualquier ticker a demanda), con medias móviles, RSI de Wilder con **divergencias marcadas en la vela en que se pudieron operar**, MACD, Bollinger, Ichimoku, perfil de volumen y **soportes y resistencias en zonas** (confluencia diario/semanal/mensual, con R:R neto de comisiones). El backtest de señales mide el exceso sobre entrar en cualquier vela y corrige por pruebas múltiples: ninguna señal clásica agrega valor demostrable en el conjunto, y la pantalla lo dice.

<p align="center"><img src="assets/screenshots/liquidez_charting_grafico_velas_sma_rsi.webp" alt="Charting: velas diarias de SPY con medias móviles, RSI con divergencias, MACD, volumen y lectura técnica" width="100%"><br><sub>Charting: velas diarias de SPY con medias móviles, RSI con divergencias, MACD, volumen y lectura técnica</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_charting_niveles_zonas_confluentes.webp" alt="Niveles: zonas de soporte y resistencia con confluencia diaria/semanal/mensual, perfil de volumen y tabla accionable con R:R neto" width="100%"><br><sub>Niveles: zonas de soporte y resistencia con confluencia diaria/semanal/mensual, perfil de volumen y tabla accionable con R:R neto</sub></p>

<p align="center"><img src="assets/screenshots/liquidez_charting_backtest_de_senales.webp" alt="Backtest de señales del charting con comisiones: n, rendimiento neto, exceso sobre entrar en cualquier vela y curva de capital" width="100%"><br><sub>Backtest de señales del charting con comisiones: n, rendimiento neto, exceso sobre entrar en cualquier vela y curva de capital</sub></p>

### Salud de datos y reloj de mercados
Panel permanente de salud de datos: frescura de cada fuente contra su propia frecuencia de publicación, claves duplicadas, fechas inconsistentes, saltos anómalos y semáforos por serie. Reloj de mercados con banderas y solapes de rueda arriba de todo el escritorio.

<p align="center"><img src="assets/screenshots/inicio_reloj_mercados_del_mundo.webp" alt="Reloj de mercados del mundo: hora, estado de la rueda y solapamientos de 9 mercados + cripto" width="100%"><br><sub>Reloj de mercados del mundo: hora, estado de la rueda y solapamientos de 9 mercados + cripto</sub></p>

<p align="center"><img src="assets/screenshots/sidebar_salud_de_datos.webp" alt="Panel «Salud de datos» de la barra lateral: semáforos y avisos por fuente" width="35%"><br><sub>Panel «Salud de datos» de la barra lateral: semáforos y avisos por fuente</sub></p>

### Mi cartera — tenencias, riesgo y calificaciones
Diario de operaciones con precio neto de comisión y TIR de compra con el pricer real, tenencias por FIFO con valor de liquidación y TIR del dinero, riesgo por factor y estrés, calificaciones de las agencias locales leídas de la CNV, swing trading en renta fija y optimizador de pesos.

La cartera que se muestra es **real**: una cartera chica (valor de liquidación de unos USD 2.100) en seis bonos —AN29, AO28, DNCAO, IRCPO, TLCTO y YM43O—, con el soberano **AO28** (~29 % de la cartera) y su **tesis de compra** registrada (por qué se compró, qué la invalida y las condiciones medibles que el sistema evalúa todos los días), y se compara contra la cartera que sugirió el Armador con criterio de Markowitz (ver arriba). Cada vista usa el pricer real: precio neto de comisión, TIR del dinero por FIFO, riesgo por factor, calificaciones de las agencias leídas de la CNV, liquidez y estrés.

<p align="center"><img src="assets/screenshots/cartera_tenencias_kpis_y_resultado.webp" alt="Mi cartera: valor de liquidación, TIR del dinero, duration y DV01" width="100%"><br><sub>Mi cartera: valor de liquidación, TIR del dinero, duration y DV01</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_posiciones.webp" alt="Mi cartera: posiciones, rendimiento, riesgo y cascada del resultado de un bono" width="100%"><br><sub>Mi cartera: posiciones, rendimiento, riesgo y cascada del resultado de un bono</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_tesis_de_cada_compra.webp" alt="Mi cartera: tesis de cada compra (la nota de AO28)" width="100%"><br><sub>Mi cartera: tesis de cada compra (la nota de AO28)</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_riesgo_y_estres.webp" alt="Mi cartera: riesgo, correlación y aporte de cada posición" width="100%"><br><sub>Mi cartera: riesgo, correlación y aporte de cada posición</sub></p>

<details>
<summary>📷 Ver las 15 capturas restantes de Mi cartera (diario, compras, historial, calificaciones, swing, rotación y fuerza relativa)</summary>

<p align="center"><img src="assets/screenshots/cartera_diario_de_operaciones.webp" alt="Mi cartera: diario de operaciones con precio neto de comisión y TIR de compra" width="100%"><br><sub>Mi cartera: diario de operaciones con precio neto de comisión y TIR de compra</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_tus_compras_fueron_buenas.webp" alt="Mi cartera: ¿fueron buenas las compras? (carry, cambio de tasa y comisiones)" width="100%"><br><sub>Mi cartera: ¿fueron buenas las compras? (carry, cambio de tasa y comisiones)</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_historial.webp" alt="Mi cartera: historial reconstruido de valor, TIR y concentración" width="100%"><br><sub>Mi cartera: historial reconstruido de valor, TIR y concentración</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_calificaciones.webp" alt="Mi cartera: calificaciones de las agencias leídas de la CNV y escala común" width="100%"><br><sub>Mi cartera: calificaciones de las agencias leídas de la CNV y escala común</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_credito_y_liquidez.webp" alt="Mi cartera: ¿y si un emisor cae en default? y días para salir" width="100%"><br><sub>Mi cartera: ¿y si un emisor cae en default? y días para salir</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_breakeven_y_sensibilidad.webp" alt="Mi cartera: TIR breakeven por bono y horizonte, y matriz de sensibilidad" width="100%"><br><sub>Mi cartera: TIR breakeven por bono y horizonte, y matriz de sensibilidad</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_flujos_y_reinversion.webp" alt="Mi cartera: calendario de cobros y plan de reinversión de cupones" width="100%"><br><sub>Mi cartera: calendario de cobros y plan de reinversión de cupones</sub></p>

<p align="center"><img src="assets/screenshots/cartera_tenencias_que_hacer.webp" alt="Mi cartera: lectura automática de qué hacer" width="100%"><br><sub>Mi cartera: lectura automática de qué hacer</sub></p>

<p align="center"><img src="assets/screenshots/cartera_swing_hoy_fundamentos_valuacion_y_senales.webp" alt="Swing en renta fija: fundamentos macro, valuación relativa y señales" width="100%"><br><sub>Swing en renta fija: fundamentos macro, valuación relativa y señales</sub></p>

<p align="center"><img src="assets/screenshots/cartera_swing_rotacion_de_nominales.webp" alt="Rotación de nominales: canje de pares AL/GD con edge neto de costos" width="100%"><br><sub>Rotación de nominales: canje de pares AL/GD con edge neto de costos</sub></p>

<p align="center"><img src="assets/screenshots/cartera_swing_evidencia_historica_de_aciertos.webp" alt="Swing: evidencia histórica de aciertos con corrección por pruebas múltiples" width="100%"><br><sub>Swing: evidencia histórica de aciertos con corrección por pruebas múltiples</sub></p>

<p align="center"><img src="assets/screenshots/cartera_swing_seguimiento_hacia_adelante.webp" alt="Swing: seguimiento hacia adelante en vivo" width="100%"><br><sub>Swing: seguimiento hacia adelante en vivo</sub></p>

<p align="center"><img src="assets/screenshots/cartera_fuerza_relativa_y_rotacion.webp" alt="Fuerza relativa y rotación de tus bonos" width="100%"><br><sub>Fuerza relativa y rotación de tus bonos</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_11_comparar_contra_tu_cartera_actual.webp" alt="Comparación contra la cartera actual y plan de rebalanceo en nominales, con comisiones" width="100%"><br><sub>Comparación contra la cartera actual y plan de rebalanceo en nominales, con comisiones</sub></p>

<p align="center"><img src="assets/screenshots/armador_markowitz_12_panel_de_riesgo_desvio_historico.webp" alt="Panel de riesgo: desvío histórico anualizado por bono" width="100%"><br><sub>Panel de riesgo: desvío histórico anualizado por bono</sub></p>

</details>

## Qué resuelve

Antes de este sistema, armar la foto diaria del mercado de renta fija argentino significaba actualizar planillas de Excel a mano: bajar precios, recalcular TIR y duration bono por bono, y cruzar manualmente con la macro del día. Este sistema automatiza esa cadena completa y agrega capacidades que no existían en la versión manual: ajuste de curvas de rendimiento elegidas por backtest, detección de arbitraje legislativo y de arbitraje FX sintético con acción sugerida, y un panel de riesgo sistémico global actualizado en vivo — todo cruzado contra el contexto macro real de Argentina y del mundo, no solo el precio del bono en el vacío.

## Un día de trabajo de un analista

El sistema está pensado alrededor del flujo real de una mesa o de un research desk, de la apertura a la entrega:

| Momento | Qué usa | Qué obtiene |
|---|---|---|
| **Antes de la apertura** | Brief matutino, **Qué cambió hoy**, alertas de curva | Banderas de riesgo (macro global y riesgo Argentina), cuadro de 6 dólares con brecha, contexto overnight (futuros de EE.UU., Asia, DXY, agro), próximas publicaciones, cambio de TIR por clase contra la rueda anterior y alertas con umbrales editables |
| **Durante la rueda** | Precios y puntas cada 30 minutos, refresco automático, reloj de mercados, tablas con click a la ficha | Pricing al día sin relanzar nada (la sección de Renta Fija se refresca sola cada 2 minutos), MEP y brecha en vivo, tablero de arbitraje con acción sugerida |
| **Valuar y comparar** | Curvas de rendimiento por clase (logarítmica; Nelson-Siegel en Dollar Linked), Z-spread, comparables, valor relativo, forwards | Dónde está caro o barato cada bono contra su curva, contra su par de legislación y contra su propia historia |
| **Decidir** | Breakeven (total return), carry trade completo, radar, simulador, armador de portafolios | Retorno neto de comisiones de comprar hoy y salir en una fecha, con escenarios de TIR de salida, dólar y reinversión |
| **Seguir la cartera** | Tenencias, riesgo y estrés, calificaciones, tesis y alertas | TIR del dinero, DV01, escenarios de curva, estrés histórico, cambios de calificación y tesis invalidadas |
| **Entender el contexto** | 14 vistas de macro Argentina y el escritorio global | La macro cruzada con el precio: fiscal, bancos, reservas, inflación, actividad, externo y expectativas |
| **Entregar** | Ficha Excel por instrumento, exportación masiva, matriz a Excel y **Research Report en PDF** | Fichas con el cronograma y el valor presente de cada flujo, y un reporte listo para enviar a un cliente o inversor: resumen ejecutivo con banderas, contexto macro y cambiario, las curvas de rendimiento y métricas tácticas |
| **Confiar en el dato** | Panel de salud de datos, etiqueta de base del precio, fecha de cada serie | Cada precio dice si viene de puntas reales o del último operado, y cada fuente dice qué tan fresca está |

## Motores de pricing

Cobertura de 7 tipos de instrumentos de renta fija argentina, sobre un universo de más de 200 activos (36 soberanos/provinciales + 189 Obligaciones Negociables corporativas + Letras y bonos en pesos):

- Tasa fija en pesos (soberana — LECAPs/BONCAP/BADLAR/TAMAR)
- CER (ajustados por inflación — lookup a T-10 sobre CER Base y CER de liquidación/vencimiento, más un proyector propio que extiende la curva a futuro con el REM cuando aún no hay CER oficial publicado)
- Dollar-linked
- Duales (con ruteo automático por pata ganadora)
- Hard dollar soberano y provincial (USD, ley local y extranjera)
- Hard dollar corporativo (Obligaciones Negociables en USD, ley local y extranjera)
- Corporativos y provinciales en ARS a tasa fija/step-up (sin indexación CER/UVA/dollar-linked)

Cálculos incluidos: NPV, Duration Macaulay/Modificada, Convexidad, Z-spread generalizado (contra la curva del Tesoro americano interpolada para instrumentos en USD, y contra una curva libre-de-riesgo propia en pesos —construida en vivo a partir de LECAPs/BONCAPs— para instrumentos en ARS), y solvers de TIR propios — un **solver por método de Brent optimizado con Numba (JIT)** para los instrumentos en pesos (tasa fija, CER, duales), y `scipy.optimize.brentq` para los bonos hard-dollar.

## Comisiones y vistas Mercado / Compra / Venta

Un bono no rinde lo mismo para quien lo compra hoy que para quien lo vende hoy, y ningún pricer de planilla lo refleja. Todos los motores y todo el dashboard calculan cada métrica en **tres vistas**:

| Vista | Precio | Flujos | Qué mide |
|---|---|---|---|
| **Mercado** | último precio operado | brutos | el bono sin fricción |
| **Compra** | punta vendedora (ask real de IOL) más la comisión, sobre precio sucio | cupones netos de la comisión de renta | lo que rinde quien entra hoy y mantiene |
| **Venta** | punta compradora menos la comisión | — | lo que cobrás si vendés hoy |

- **Tarifas verificadas contra la fuente primaria** del broker (texto crudo de su página), con IVA y derechos de mercado como parámetros; perfiles de Balanz e IOL seleccionables y editables desde la interfaz.
- **Ningún default silencioso**: un instrumento que no se puede clasificar en su clase de arancel recibe la tarifa más alta, queda etiquetado y hace fallar un test.
- **Una sola fórmula de retorno con costos**, compartida por Breakeven, Radar, Simulador, alertas y fichas (antes estaba copiada en cuatro lugares).
- **Efecto que casi nadie mide**: la fricción pesa mucho en plazos cortos anualizada. Una letra a 30 días con 0,24% de ida y vuelta puede perder 3 puntos de TEA; por eso el sistema calcula los **días que tarda el carry en pagar la comisión**.
- Las columnas de la vista Mercado son **bit-idénticas** a las anteriores a este cambio (test de regresión contra el pricer viejo).

## Armador de portafolios: Markowitz con restricciones reales

Construye una cartera **hard dollar** (soberanos y Obligaciones Negociables en USD) a partir de un objetivo y de las restricciones de un inversor, y devuelve **nominales a comprar** con las comisiones incluidas: el monto que se carga es el desembolso total.

- **Tres objetivos**: *Target Duration* (con tolerancia), *Target Yield* y *Óptimo de Markowitz* (máximo Sharpe, o mínima varianza pura como referencia).
- **Restricciones y filtros**: volumen mínimo operado hoy, soberanos y/o corporativos, cantidad mínima y máxima de bonos, **tope por emisor y por sector**, TIR mínima, duration fija con tolerancia, preferencia por liquidez y **frecuencia de cobro como restricción dura** (la cartera tiene que pagar cupón o amortización en cada mes elegido; si el universo no lo permite, avisa qué mes queda sin cobertura antes de optimizar).
- **Método**: *Target Duration* y *Target Yield* se resuelven con programación lineal entera (MILP con HiGHS), que sí puede imponer la cantidad de bonos; Markowitz usa pesos continuos (SLSQP) sobre los bonos con historia de precios suficiente, con covarianza muestral de los retornos diarios en dólares. Para respetar la cantidad de bonos elegida se preselecciona por Sharpe individual respetando los topes de cada grupo, y antes de optimizar se verifica que las restricciones sean compatibles: si no lo son, informa que no es factible, con un mensaje explicable, en vez de fallar.
- **Métricas rigurosas**: la TIR y la duration de la cartera son las del **flujo de fondos agregado** (como en Fabozzi, *The Handbook of Fixed Income Securities*), no el promedio ponderado de cada bono; se muestran lado a lado para ver la diferencia.
- **Qué devuelve**: composición con peso, monto, nominales y precio neto de compra; costo de implementación y TIR de ida y vuelta a un horizonte elegido; calendario de flujos por bono; por qué esa combinación; estrés de tasa con re-descuento completo; correlación entre los bonos; frontera eficiente; backtest retroactivo con VaR, CVaR y Sortino; panel de riesgo por bono; y exclusión de bonos con recálculo. Dos auditorías automáticas quedan en pantalla: que ningún bono se aleje más de ±300 pb de la curva de su emisor o sector, y que todos tengan clase de arancel.
- **Comparar contra tu cartera**: un botón carga lo registrado en *Mi cartera* y arma el **plan de rebalanceo en nominales**, con la comisión de cada pata. Si la TIR de la cartera sugerida no supera a la actual una vez descontado el valor de liquidación, lo dice («rotar solo suma costo»), en vez de empujar a operar.
- **Límites que muestra el propio sistema**: Markowitz no es una frontera con cardinalidad exacta sobre todo el universo (pesos continuos sobre un conjunto preseleccionado); las métricas de riesgo y el backtest cubren solo los bonos con al menos 50 % de cobertura de precios en los últimos 12 meses (en el ejemplo de las capturas, 7 de 9) y el aviso aparece en pantalla; y es una herramienta de análisis, no una recomendación de inversión.

Las capturas de una corrida real (USD 10.000, volumen mínimo 100.000, tope 25 % por emisor y 30 % por sector, 6 a 10 bonos) están en [Armador con optimización de Markowitz](#armador-con-optimización-de-markowitz--cartera-de-9-bonos-con-topes-por-emisor-y-por-sector).

## Terminal de analista: valor relativo, TIR histórica y señales

- **TIR histórica diaria reconstruida con los pricers reales**: cada precio pasado se valúa con la misma matemática del dashboard en vivo y solo con lo que se sabía ese día (CER publicado, REM vigente, TAMAR hasta el día anterior). Contrastada: con los precios de hoy reproduce 58 de 58 TIR del pricer vivo con diferencia de 0,00 pb. Incluye 101 instrumentos en pesos ya vencidos para evitar el **sesgo de supervivencia**.
- **Curva de rendimiento elegida con evidencia**: logarítmica por defecto (Nelson-Siegel solo en Dollar Linked), con mínimo de 5 bonos y calidad de ajuste visible en cada panel; el backtest es reproducible (`tools/backtest_curvas.py`).
- **Valor relativo**: spreads de legislación con estudio de eventos sin mirar el futuro, vida media de reversión y edge neto de comisiones; caro/barato contra la curva soberana Nelson-Siegel (cada soberano se mide contra la curva sin él mismo).
- **Forwards implícitas, roll-down y yield USD sintético** (paridad cubierta con futuros de dólar), siempre limitados a donde la curva llega.
- **Carry trade como operación completa** con las cuatro patas de conversión y breakeven contra REM, ROFEX y banda cambiaria.
- **Archivo intradía** de precios (no existía historia del día) y **respaldo semanal verificado** de lo que no se puede volver a bajar.
- **Backtests honestos**: la señal usa datos hasta el cierre de t, se entra en la apertura de t+1, se descuentan comisiones y se mide el exceso contra la base del mismo activo.

## Fiscal, sistema bancario y reservas con datos oficiales

Tres vistas de Macro Argentina construidas sobre fuentes estructuradas (API, CSV y Excel), con identidades contables verificadas en cada lectura y contraste contra una segunda fuente cuando existe.

- **🏛️ Reservas**: escalera de reservas brutas → netas tipo FMI → netas líquidas, verificada contra el NIR publicado por el FMI (diferencias de USD 27 y 60 millones a fines de 2024 y 2025). La composición oficial y la liquidez a un año salen de la **API de datos del FMI** (misma planilla SDDS que publica el BCRA, en formato máquina: 92 meses desde 2019 con todas las identidades verificadas).
- **🧾 Fiscal**: recaudación total y por impuesto, resultado primario y financiero (en pesos reales y % del PIB, que reproduce el dato oficial de 1,8% en 2024 y 1,4% en 2025), deuda y financiamiento del Tesoro, previsional, cuasifiscal y empleo registrado. Con una advertencia importante que casi todo analista pasa por alto: **la recaudación total de ARCA no es recaudación fiscal** (incluye obras sociales, ART y otros fondos que no van al Tesoro).
- **🏧 Crédito y bancos**: crecimiento real de crédito y depósitos calculado a partir de los saldos mensuales del BCRA y contrastado contra el Informe sobre Bancos (depósitos dentro de 0,2 puntos porcentuales); calidad de cartera por línea desde 2010; solvencia y rentabilidad por grupo de entidades.
- **Menos PDF, más datos**: se reemplazó la lectura de PDF por fuentes estructuradas siempre que existen. El contraste contra el PDF anterior encontró dos errores del parser viejo (ver [Rigor de ingeniería](#rigor-de-ingeniería-y-control-de-calidad)).

## Mi cartera: tenencias, riesgo y calificaciones

El seguimiento de una cartera propia con el mismo rigor que el pricing:

- **Diario de operaciones** con precio neto de comisión (real o del modelo, marcando el origen) y **TIR de compra con el pricer real** a la fecha de cada operación.
- **Tenencias por FIFO**: valor de liquidación (neto de comisión), cobros asignados al lote que los recibió, **TIR del dinero** (una sola tasa que iguala lo pagado en cada compra, con su fecha) y XIRR; cada compra se descompone en carry, cambio de tasa y comisiones.
- **Riesgo y estrés**: descomposición de Euler del riesgo por posición y por factor (inflación, dólar, soberano USD, crédito, tasa en pesos), escenarios de curva con **reprecio exacto** flujo por flujo, VaR y ES, y estrés histórico con los episodios reales del riesgo país (PASO 2019, COVID, julio 2022, balotaje 2023...).
- **Calificaciones**: lee de la CNV los dictámenes de las agencias locales (FIX, Moody's Local, S&P Argentina), normaliza las tres notaciones a una escala común y detecta cambios. La escala internacional va en una tabla manual: esos sitios exigen registro y el sistema **no los esquiva**.
- **Swing trading en renta fija**: hipótesis **definidas antes de correrlas**, con corrección por pruebas múltiples y resultados negativos publicados (una de las tres hipótesis iniciales pierde dinero en todas las configuraciones y se descartó). El monitor **avisa, no opera**.
- **Optimizador** de pesos objetivo (carry menos aversión por varianza menos costo de llegar, con covarianza Ledoit-Wolf), **tesis por operación** con condiciones medibles evaluadas todos los días y alertas.

## Exportación a Excel: fichas de nivel research desk

> Además del Excel, el escritorio genera un **Research Report en PDF** (resumen ejecutivo con banderas de riesgo, contexto macro y cambiario, las nueve curvas de rendimiento y métricas tácticas) pensado para enviarse a un cliente o inversor; reutiliza los mismos gráficos y cálculos que ve el analista en pantalla, nunca un constructor paralelo.

Cada instrumento se puede exportar a un Excel completo con la misma metodología y los mismos
números que el dashboard en vivo — nunca una fórmula paralela: reusa el motor de pricing, con
un flag que además le hace devolver el cronograma detallado fila por fila.

- **Cronograma completo con Valor Presente de cada flujo** (PVCF), con una fila de control que
  reconcilia la suma de los PVCF contra el precio efectivamente pagado — la misma estructura
  que arma un analista a mano en un modelo de manual de finanzas.
- **Cronograma en pesos NOMINALES, no en unidades de valor nominal (VN) constantes**: para
  instrumentos CER, cada cupón/amortización futuro se multiplica por el coeficiente CER
  proyectado a ESA fecha puntual (no un único coeficiente fijo de hoy), así un cupón
  contractualmente parejo se ve crecer en $ a medida que se aleja en el tiempo; para Dollar
  Linked, cada flujo se muestra en USD y en ARS al tipo de cambio proyectado de ese día bajo
  cada uno de los 4 escenarios. La TIR/Duration/Convexidad y el análisis de sensibilidad se
  siguen resolviendo en términos reales — separar ambas vistas evita el error clásico de leer
  "100" en una fila de un bono indexado y pensar que ese es el dinero real que se cobra.
- **Análisis de sensibilidad**: tabla y gráfico de Precio vs. TIR en un rango de ±10 puntos
  porcentuales, con la aproximación clásica de Duration-Convexidad superpuesta contra el
  reprecio exacto — muestra en qué rango de movimiento de tasa la aproximación lineal de manual
  sigue siendo confiable y dónde empieza a divergir del valor real.
- **DV01** en dos escalas (por 100 de nominal y por una posición real de VN 1.000.000), **Vida
  Promedio** (average life, distinta de Duration), **spread contra la curva propia** del
  emisor, y **comparables** — los bonos de duration más parecida dentro de la misma clase de
  emisor, más una tabla aparte con otros instrumentos del mismo emisor exacto (primer chequeo
  de concentración de un analista de riesgo).
- **Detalle completo del prospecto**: emisor, ISIN, ley, tipo de amortización, frecuencia de
  pago, base de cálculo, y las fechas de referencia exactas usadas para tasas variables
  (TAMAR/BADLAR) o ajuste por CER (CER Base/Liquidación a T-10, coeficiente aplicado).
- **Riesgo, ejecución y contexto** (el mismo bloque en todas las clases de instrumento): base del precio (con puntas reales de IOL o último operado), precio Bid y Ask, costo de ida y vuelta y días para recuperar
  la comisión, volumen operado hoy y hora del precio, dólar MEP implícito (Hard Dollar), **próximo pago**, **breakeven a +3/+6/+12 meses** en las tres vistas de precio, calificaciones del emisor y **contexto de
  la TIR** (percentil contra su último año y, en soberanos, el spread contra su par de legislación con su z-score). Cada dato se muestra solo si existe para ese instrumento: nunca se rellena con un valor inventado.
- **Exportación masiva**: un botón arma un .zip con una ficha por cada instrumento vigente del
  universo completo, organizadas en carpetas por tipo y emisor, más un archivo "Global" por
  clase de instrumento con una hoja Resumen — hipervínculos a la hoja de cada ticker y escala
  de color condicional incluidos — para tener el book completo de renta fija en un solo click.

Validado con tests (pytest) que reconcilian, para todo el universo vigente, el PVCF
exportado contra el precio de mercado y contra el valor que muestra el dashboard en vivo —
ninguna de las dos vistas puede divergir de la otra sin que un test lo detecte.

### Ejemplos de fichas exportadas

Cada ficha trae, en una sola hoja: métricas de sensibilidad, las tres vistas de precio, el detalle del prospecto, el cronograma con el valor presente de cada flujo, el análisis de sensibilidad con su gráfico y los comparables. El pie de cada hoja indica la fecha y hora de generación.

**Hard Dollar (soberanos, provinciales y ONs)** — ejemplo: el **GD46** (soberano ley Nueva York, amortizable en 40 pagos). Cronograma en USD, con el control que reconcilia la suma de los PVCF contra el precio pagado.

<p align="center"><img src="assets/screenshots/excel_ficha_hard_dollar_GD46_metricas_vistas.webp" alt="Ficha Excel GD46: métricas financieras y vistas de precio Mercado / Compra / Venta con la tarifa aplicada" width="100%"><br><sub>Ficha Excel GD46: métricas financieras y vistas de precio Mercado / Compra / Venta con la tarifa aplicada</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_hard_dollar_GD46_riesgo_contexto.webp" alt="Ficha Excel GD46: riesgo, ejecución y contexto de la TIR (bid/ask, fricción, volumen, MEP/CCL implícito, breakeven y percentil histórico)" width="100%"><br><sub>Ficha Excel GD46: riesgo, ejecución y contexto de la TIR (bid/ask, fricción, volumen, MEP/CCL implícito, breakeven y percentil histórico)</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_hard_dollar_GD46_prospecto_cronograma.webp" alt="Ficha Excel GD46: detalle del prospecto y cronograma de 40 pagos con PVCF, flujos netos de la vista Compra y fila de totales" width="100%"><br><sub>Ficha Excel GD46: detalle del prospecto y cronograma de 40 pagos con PVCF, flujos netos de la vista Compra y fila de totales</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_hard_dollar_GD46_sensibilidad.webp" alt="Ficha Excel GD46: análisis de sensibilidad del precio a la TIR (±10 pp) con su gráfico" width="100%"><br><sub>Ficha Excel GD46: análisis de sensibilidad del precio a la TIR (±10 pp) con su gráfico</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_hard_dollar_GD46_comparables_repago.webp" alt="Ficha Excel GD46: comparables por duration y perfil de repago" width="100%"><br><sub>Ficha Excel GD46: comparables por duration y perfil de repago</sub></p>

**CER** — cronograma en **pesos nominales**: cada flujo usa el coeficiente CER proyectado a su propia fecha de pago, así un cupón contractualmente parejo se ve crecer en pesos; la TIR y la sensibilidad siguen en términos reales.

<p align="center"><img src="assets/screenshots/excel_ficha_cer_TX28_metricas_vistas.webp" alt="Ficha Excel CER (TX28): métricas y vistas" width="100%"><br><sub>Ficha Excel CER (TX28): métricas y vistas</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_cer_TX28_prospecto_cronograma.webp" alt="Ficha Excel CER (TX28): cronograma nominal con coeficiente CER" width="100%"><br><sub>Ficha Excel CER (TX28): cronograma nominal con coeficiente CER</sub></p>

**Tasa Fija / LECAP / BONCAP** — flujos a tasa fija en pesos, con TEA, TNA y TEM y las tres vistas de precio.

<p align="center"><img src="assets/screenshots/excel_ficha_tasa_fija_S30O6_metricas_vistas.webp" alt="Ficha Excel Tasa Fija (S30O6): métricas y vistas" width="100%"><br><sub>Ficha Excel Tasa Fija (S30O6): métricas y vistas</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_tasa_fija_S30O6_prospecto_cronograma.webp" alt="Ficha Excel Tasa Fija (S30O6): cronograma" width="100%"><br><sub>Ficha Excel Tasa Fija (S30O6): cronograma</sub></p>

**Dollar Linked** — cada flujo en USD y en ARS al tipo de cambio proyectado de ese día bajo cada uno de los cuatro escenarios (REM, ROFEX, A3500 y Banda).

<p align="center"><img src="assets/screenshots/excel_ficha_dollar_linked_TZV27_metricas_vistas.webp" alt="Ficha Excel Dollar Linked (TZV27): métricas y escenarios" width="100%"><br><sub>Ficha Excel Dollar Linked (TZV27): métricas y escenarios</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_dollar_linked_TZV27_prospecto_cronograma.webp" alt="Ficha Excel Dollar Linked (TZV27): cronograma" width="100%"><br><sub>Ficha Excel Dollar Linked (TZV27): cronograma</sub></p>

**Corporativos y provinciales en ARS, y Duales** — mismas secciones, con el ruteo por pata ganadora en los duales.

<p align="center"><img src="assets/screenshots/excel_ficha_corporativo_ars_RVS1O_metricas_vistas.webp" alt="Ficha Excel corporativo ARS (RVS1O): métricas y vistas" width="100%"><br><sub>Ficha Excel corporativo ARS (RVS1O): métricas y vistas</sub></p>

<p align="center"><img src="assets/screenshots/excel_ficha_corporativo_ars_RVS1O_prospecto_cronograma.webp" alt="Ficha Excel corporativo ARS (RVS1O): cronograma" width="100%"><br><sub>Ficha Excel corporativo ARS (RVS1O): cronograma</sub></p>

**Archivo "Global" por clase** — hoja *Resumen* con hipervínculos a la hoja de cada ticker, escala de color condicional en TIR y paridad, y la curva de rendimiento por grupo de emisor.

<p align="center"><img src="assets/screenshots/excel_global_hard_dollar_resumen.webp" alt="Excel Global Hard Dollar: hoja Resumen con escala de color y vínculos a cada ficha" width="100%"><br><sub>Excel Global Hard Dollar: hoja Resumen con escala de color y vínculos a cada ficha</sub></p>

<p align="center"><img src="assets/screenshots/excel_global_tasa_fija_resumen.webp" alt="Excel Global Tasa Fija: hoja Resumen" width="100%"><br><sub>Excel Global Tasa Fija: hoja Resumen</sub></p>

<p align="center"><img src="assets/screenshots/excel_global_cer_resumen.webp" alt="Excel Global CER: hoja Resumen" width="100%"><br><sub>Excel Global CER: hoja Resumen</sub></p>

**Research Report en PDF** — el reporte para enviar a un cliente o inversor: resumen ejecutivo con las banderas de riesgo, contexto macro y cambiario, las curvas de rendimiento y las métricas tácticas. Usa los mismos gráficos y cálculos que ve el analista en pantalla.

<p align="center"><img src="assets/screenshots/pdf_research_report_1_resumen_ejecutivo.webp" alt="Research Report en PDF: resumen ejecutivo con banderas de riesgo" width="100%"><br><sub>Research Report en PDF: resumen ejecutivo con banderas de riesgo</sub></p>

<p align="center"><img src="assets/screenshots/pdf_research_report_2_curva_tasa_fija_y_metricas.webp" alt="Research Report en PDF: curva de rendimiento de Tasa Fija y métricas" width="100%"><br><sub>Research Report en PDF: curva de rendimiento de Tasa Fija y métricas</sub></p>

<p align="center"><img src="assets/screenshots/pdf_research_report_3_metricas_hard_dollar_soberanos.webp" alt="Research Report en PDF: métricas de soberanos y provinciales en dólares" width="100%"><br><sub>Research Report en PDF: métricas de soberanos y provinciales en dólares</sub></p>

<p align="center"><img src="assets/screenshots/pdf_research_report_4_matriz_de_breakeven.webp" alt="Research Report en PDF: matriz de breakeven" width="100%"><br><sub>Research Report en PDF: matriz de breakeven</sub></p>

## Otros motores destacados

- **Calculadora de FX implícito (MEP/CCL)**: prioriza fuente de precio (API de bróker → ratio AL30/GD30 → A3500) y descarta automáticamente cotizaciones con más de 30 días de antigüedad.
- **Motor de régimen macro** *(prototipado, hoy desactivado en producción)*: matriz diaria de régimen con un *firewall* anti-look-ahead-bias (retrasa 2 días las variables del BCRA para simular el rezago real de publicación), un "Shadow FX" (tipo de cambio implícito por M2/reservas) y un Z-score móvil (ventana de 90 días) del spread interbancario BAIBAR-TAMAR. El código está intacto pero deshabilitado desde una auditoría que encontró 19 variables sin alimentar (tras un cambio de esquema de nombres de archivo, sin ningún consumidor en el dashboard) — retomarlo como feature real requiere remapear esas 19 variables a los archivos reales de BCRA, uno por uno.
- **Semáforo de Estrés Sistémico Global (GSSI)**: índice compuesto 0-100 por percentil histórico de VIX, MOVE, VVIX y CISS, con su variación mensual como referencia de tendencia.
- **Proyector de CER por REM**: cuando el BCRA todavía no publicó el CER oficial de una fecha futura, bootstrapea la curva de inflación mensual implícita en el REM (Relevamiento de Expectativas de Mercado) y la proyecta día a día, respetando qué mes de IPC corresponde legalmente aplicar según el día del mes.
- **Proyector de banda cambiaria con jerarquía de 3 fuentes** (motor compartido entre Dollar Linked y Carry Trade): el ritmo diario de ensanchamiento de la banda replica empíricamente la inflación INDEC de dos meses antes (T-2) — el mismo rezago que usa el propio régimen cambiario oficial — así que se usa esa inflación real ya publicada mientras esté disponible; recién cuando el T-2 real de un mes todavía no salió (los ~2 meses más próximos a hoy, por construcción del rezago) cae a la expectativa de inflación del REM para ese mismo mes; solo como último recurso, más allá también del horizonte REM, repite la última inflación real conocida. Nunca deja la banda "congelada" en un valor viejo pudiendo usar algo mejor informado, y el dashboard distingue visualmente qué tramo de cada curva es dato firme y cuál es proyección.
- **Scraping de INDEC gateado por su propio calendario oficial**: en vez de scrapear los más de 25 módulos activos todos los días, el sistema parsea el PDF de calendario de difusión semestral de INDEC (layout de 2 columnas, reconstruido por posición de palabra), le suma las fechas reales de publicación que figuran en la web del instituto y solo va a buscar cada módulo el día que le corresponde según ese calendario — con margen de tolerancia y un modo *fail-open* que nunca deja de scrapear algo por un mapeo incompleto.
- **Deuda Externa Bruta, recuperada de su fuente real vigente**: el reporte standalone de Deuda Externa de INDEC está discontinuado desde 2017 (verificado navegando la página oficial en vivo); la serie completa 2006-hoy sigue publicándose, pero integrada dentro del informe combinado de Cuentas Internacionales — el parser se extendió para extraerla de ahí, en vez de asumir que "no hay dato" por seguir apuntando a la página vieja.
- **Unificación de fuentes oficiales por sobre agregadores de terceros**: antes de migrar una serie (inflación, tasa de depósitos) de un agregador externo a la API oficial del BCRA, se verifica en vivo que ambas fuentes den exactamente el mismo historial — la migración se hace solo cuando la oficial es al menos igual de completa (y, en algún caso, más actualizada), nunca a ciegas por preferencia.
- **Gestión de cuota de API con circuit-breaker**: el consumo mensual de la API de precios de mercado se trackea en una base local; si se acerca al límite del plan gratuito, el sistema corta las llamadas antes de arriesgar un bloqueo, en vez de descubrirlo a mitad de mes con el servicio caído. En números: ~22 llamadas por corrida de precios intradiaria, ~6.000/mes de consumo real contra un límite gratuito de 25.000/mes (24% de uso), con alerta al 90% y corte duro al 100%.

## Hallazgos de análisis (con sus límites)

Seis análisis pensados para **no engañarse**: cada uno informa n, el estadístico, el azar esperable y, cuando se prueban muchas combinaciones, corrige por pruebas múltiples. Resultados descriptivos al 05/10/2026 (los tests fijan el método, no los números); precedencia estadística no es causalidad.

- **Señales técnicas clásicas**: sobre 64 activos y 545 pruebas con evidencia suficiente, 23 resultan "significativas" al 5% sin corregir (se esperan ~27 por azar) y **0 sobreviven a la corrección de Benjamini-Hochberg**. Ninguna agrega valor demostrable en el conjunto.
- **¿Los soberanos siguen al Tesoro?** No: la tasa del Tesoro de igual duration explica ~1% de la variación semanal de la TIR de los soberanos (beta indistinguible de cero). Se mueven casi por completo por riesgo propio.
- **Riesgo país: global vs local**: los factores globales (VIX, MOVE, high yield, dólar) explican ≈3% de la variación semanal, ≈11% de la mensual y ≈15% de la trimestral; el resto es local (y es una cota: incluye ruido de medición).
- **Dinero e inflación depende del régimen**: en 2003-2017 el dinero precede a la inflación (y sobrevive a los controles por tipo de cambio y actividad); desde 2018 es la inflación la que precede al dinero. La velocidad del dinero cayó ~60% entre 2004 y 2017 y subió ~97% desde 2018, así que no hay una relación fija.
- **Reservas y riesgo país**: la relación existe en la historia larga y crece con el horizonte (r de −0,13 a un mes a −0,51 a doce), pero desde 2020 no es significativa (los saltos de reservas vienen de desembolsos y swaps). Una primera versión del análisis concluía "sin relación" por mirar solo cambios mensuales desde 2020: error de método, no de dato, y quedó documentado.
- **¿Qué curva de rendimiento usar?** Para cada día y cada tipo de instrumento (14 grupos, 3.717 curvas-día desde 2024) se ajustó cada modelo sin un bono y se midió cuánto fallaba al predecirlo. La **logarítmica** de 2 parámetros quedó igual o mejor en todos los grupos (error agregado 246 pb contra 369 pb de Nelson-Siegel), que nunca ganó de forma significativa y perdió en 8 grupos; Nelson-Siegel-Svensson (6 parámetros) fue peor que Nelson-Siegel, y como la curva se reajusta cada 30 minutos la logarítmica además cambia entre un 30 % y un 55 % menos de un día al siguiente. Límites: la historia en pesos es reconstruida, CER se probó solo con Boncer a Descuento, Bopreal fue la excepción (sin significancia clara) y Dollar Linked se dejó en Nelson-Siegel porque no hay historia diaria para validarlo. El R² en muestra no sirve para elegir: subir parámetros lo acerca a 1 a costa del desvío real.
- **Carry trade**: convertir pesos a dólares y volver cuesta ~2,4% de ida y vuelta (0,605% por pata, cuatro patas): el carry en una LECAP de pocos meses no lo paga salvo que el dólar se mueva poco.

## Rigor de ingeniería y control de calidad

El sistema pasa por rondas periódicas de auditoría de código propia — no solo revisión de estilo, sino verificación de que los cálculos financieros sean correctos —, respaldadas por **casi 2.000 tests automatizados** entre las dos capas (1.859 sobre el motor de pricing y el dashboard + 180 sobre el motor de descarga). Algunos hallazgos reales, con impacto financiero directo, detectados y corregidos en producción:

- **Error de signo en la sensibilidad de convexidad**: `(-0.01**2)` se evaluaba como `-0.0001` por precedencia de operadores en Python, en vez de `+0.0001` — subestimaba la ganancia de precio ante una baja de tasa de 100bps, más notorio en bonos de mayor duration/convexidad.
- **TAMAR mal mapeado (tasa efectiva en vez de nominal)**: el BCRA publica la tasa TAMAR bajo dos IDs distintos según la base de capitalización — el sistema alimentaba la tasa efectiva anual a fórmulas que asumen explícitamente la nominal, sobrestimando la capitalización de todo bono BADLAR/TAMAR y de los duales Fija/TAMAR. Corregido y verificado contra la API oficial de BCRA en vivo.
- **Loop infinito en la descarga del Tesoro americano**: un error de anidado dejaba la lógica de paginación inalcanzable — cualquier descarga exitosa colgaba la tarea programada diaria de forma indefinida. Detectado en una auditoría previa a un deploy, corregido y verificado con timeout.
- **Catálogo de BCRA con fecha desactualizada en silencio**: para ciertas series que el BCRA precalcula con fórmula pública (como el CER), el catálogo resumen queda pisado en la fecha de hoy aunque el endpoint de datos ya tenga semanas más de historia publicada — sin el bypass correspondiente, el motor de pricing se quedaba atrás en un input crítico para el cálculo, sin ningún error visible.
- **TNA capitalizada como si ya fuera una TEA anual**: en el simulador de reinversión de cupones de la Matriz de Breakeven, una tasa de caución/money market (TNA, se renueva cada pocos días) se componía directo como `(1+TNA)^años` — la fórmula de una TEA que compone una sola vez al año. A 4.9 años de horizonte, el factor de capitalización quedaba en 4.35x en vez del 5.42x correcto (TNA→TEA vía capitalización mensual). Encontrado auditando la lógica de reinversión a pedido explícito, con un test que reproduce los números exactos del bug para blindar el fix contra una futura reversión.
- **Fecha invertida (día↔mes) en la ficha de bonos TAMAR/BADLAR**: una fecha de referencia se guardaba ya formateada como string `'dd/mm/yyyy'` (convención argentina) en vez de como objeto fecha, y el exportador la volvía a parsear sin especificar esa convención — para cualquier fecha ambigua (día ≤ 12) el resultado quedaba con día y mes invertidos en silencio (`'05/03/2026'` se mostraba como `'03/05/2026'`). Encontrado revisando en vivo los warnings de una corrida real, no en una prueba sintética.

- **Error de un parser de PDF detectado al contrastar contra la fuente estructurada**: al reemplazar la lectura de un informe en PDF por los saldos mensuales oficiales, el contraste mostró que un valor "mensual" del PDF era en realidad el interanual (7,7% contra 4,6% correcto) y que otras filas de préstamos se leían como cero sin ningún error. Ambos se corrigieron y el lector del PDF quedó solo como control opcional. Lección aplicada: priorizar CSV, Excel y APIs sobre PDF, y contrastar siempre contra una segunda fuente.
- **Lanzamientos reprogramados que la compuerta del calendario se habría salteado**: el scraper de INDEC solo va a buscar cada módulo el día que indica el calendario semestral (PDF), con ±1 día de margen. Contrastando contra las fechas reales de publicación (web del INDEC) apareció que el instituto reprograma: en septiembre de 2026, ISAC e IPI manufacturero se publicaron el 8 en lugar del 3, y el EMAE el 24 en lugar del 23. Ahora las fechas reales se unen al plan para que no se pierda ninguna.
- **Una identidad que no cerraba apuntaba a una serie sin mapear**: la planilla de reservas del FMI no cerraba sus propias identidades contables en los meses de 2019-2021 por una diferencia constante de ~USD 380 millones; era la posición de reserva en el FMI, que faltaba en el mapeo. Con ella, los 92 meses cierran sin error.
- **Un mes inconsistente en la fuente oficial, detectado y aislado**: en agosto de 2022 la masa salarial de los varones del boletín de seguridad social queda ~1,9% por debajo de lo que implican los trabajadores y el salario promedio publicados; se descarta ese mes y no el cuadro entero, y se informa en pantalla.
- **Rendimiento medido, no supuesto**: perfilando cada pantalla se encontró que la vista de deuda tardaba **95 segundos** (abría el mismo libro de Excel de nuevo por cada hoja y se llamaba dos veces). Con apertura única y una caché en disco con la firma de los archivos pasó a 15 segundos la primera vez y 0,2 segundos después; una caché por archivo bajó el resto de las vistas a menos de 1 segundo por clic. Si la descarga diaria reemplaza un archivo, la firma cambia y se vuelve a leer: nunca se sirve un dato viejo.

Estos hallazgos surgen de rondas de auditoría documentadas y fechadas contra el código real, no de pruebas puntuales — la idea es que un error de cálculo financiero se detecte por proceso, no por casualidad.

## Qué demuestra este proyecto

| Área | Evidencia concreta en este sistema |
|---|---|
| **Finanzas cuantitativas** | Pricing de 7 tipos de instrumento (TIR, duration, convexidad, Z-spread, DV01), curvas de rendimiento, forwards implícitas, roll-down, breakeven total return, VaR y ES, descomposición de Euler del riesgo, optimización con covarianza Ledoit-Wolf |
| **Conocimiento del mercado argentino** | CER con proyector por REM, Dollar Linked con bandas cambiarias, duales con ruteo de pata ganadora, arbitraje de legislación AL/GD, MEP/CCL implícito, carry trade con costo real de conversión, comisiones y derechos de mercado |
| **Ingeniería de datos** | 13 fuentes en paralelo con validación y caché incremental, warehouse DuckDB/Parquet, ~30 parsers de Excel oficiales, orquestación con Prefect, 4 tareas programadas, respaldo verificado, panel de salud de datos |
| **Rigor estadístico** | Corrección por pruebas múltiples, tests de Granger robustos, análisis sin mirar el futuro, resultados negativos publicados, distinción explícita entre precedencia y causalidad |
| **Control de calidad** | Casi 2.000 tests, contrastes contra fuentes independientes (FMI, informes oficiales), auditorías documentadas con hallazgos reales y corregidos |
| **Producto** | Una terminal con 5 secciones, ~35 pestañas y vistas pensadas para un analista: qué cambió hoy, alertas, fichas por instrumento, exportación a Excel de nivel research desk |
| **Criterio** | Cada número se puede defender, cada límite se declara en pantalla y el sistema prefiere mostrar "sin dato" a una cifra fabricada |

**Cómo trabajo con IA:** la lógica financiera, las decisiones de diseño y la verificación son propias; la implementación de código se desarrolló con asistentes de IA (Claude, Gemini). El valor está en dirigirlos con criterio y en el proceso de verificación (tests, contrastes y auditorías) que impide aceptar un resultado sin comprobarlo.

## Lógica destacada (ejemplo ilustrativo)

El código real es privado — esto es una versión simplificada, escrita para este README, que ilustra el criterio detrás de una de las señales del Tablero de Arbitraje (no es el código productivo):

```python
# Arbitraje FX sintético: Dollar Linked vs. Tasa Fija (ejemplo conceptual)
devaluacion_implicita_dl = tasa_futuro_dolar(plazo) - 1          # lo que pricea el mercado de futuros
rendimiento_tasa_fija = tir_lecap(mismo_plazo)                    # tasa fija comparable, mismo plazo

if devaluacion_implicita_dl < rendimiento_tasa_fija:
    señal = "VENDER Dollar Linked -> COMPRAR Tasa Fija"
    # el mercado de futuros está pricing menos devaluación de la que rinde
    # quedarse en pesos a tasa fija -> el Dollar Linked está "caro" en términos relativos
else:
    señal = "Sin arbitraje: la devaluación implícita ya compensa el diferencial de tasa"
```

## Stack técnico

| Capa | Tecnologías |
|---|---|
| Orquestación del pipeline de pricing | Prefect (5 fases: limpieza, ingesta a warehouse, pricing tasa fija/CER, pricing macro/FX, bonos duales, hard dollar) |
| Motor de ingesta multi-fuente | Descarga paralela propia, con caché incremental y rate-limiting por API |
| Almacenamiento analítico | DuckDB, Parquet |
| Cálculo numérico y estadística | NumPy, SciPy, Numba (JIT); Granger robusto (HC1), Newey-West, Benjamini-Hochberg, Ledoit-Wolf |
| Fuentes de datos | API BCRA, INDEC (parsers de Excel oficiales), FRED, ECB, BIS, Tesoro de EE.UU., datos.gob.ar, ARCA, Secretaría de Finanzas, API de datos del FMI (SDMX), UTDT, CNV (calificaciones), prospectos MAE (PDF), precios y puntas de IOL, otras fuentes públicas de mercado, Yahoo Finance y futuros de dólar (scraping de fuentes públicas) |
| Extracción de fichas de bonos | PyMuPDF (texto) + parsing automático por expresiones regulares del documento legal definitivo; lectura asistida por LLM en el flujo de carga manual de casos no estándar |
| Generación de reportes | openpyxl (fichas Excel completas por instrumento, exportación masiva del universo) |
| Dashboard | Streamlit, Plotly (velas, perfil de volumen), caché de lecturas por firma de archivo |
| Validación | pytest (casi 2.000 tests: 1.859 sobre el motor de pricing y el dashboard — reconciliación PVCF↔precio del exportador Excel, invariantes por familia de instrumento, controles cruzados contra fuentes independientes, paridad entre la tarea programada y la corrida manual, funciones puras 100% sintéticas — y 180 sobre el motor de descarga con sesiones simuladas sin red) + GitHub Actions (CI en cada push: compileall + pytest) |
| Automatización | Windows Task Scheduler (4 tareas: macro diaria; precios, pricing y export cada 30 min en horario de mercado; mercados globales cada 30 min; historia, respaldo y calidad semanales) con control de cuota de API |

## Roadmap

- **Control de frescura en todas las fuentes**: hoy el control por período publicado cubre los 23 módulos del INDEC (compara el último informe del calendario real de difusión con el último período de la serie); se evalúa llevarlo a las otras fuentes con calendario propio (BCRA, ARCA, Secretaría de Finanzas).
- **Armador con cardinalidad exacta en Markowitz**: hoy elige los bonos por Sharpe individual y optimiza pesos continuos sobre ese conjunto; una formulación entera con riesgo sobre todo el universo evitaría la preselección. Y ampliar la historia de precios para que los bonos recién emitidos o poco operados entren en las métricas de riesgo.
- **Más explotación de datos oficiales ya descargados**: cuadros de seguridad social por tamaño de empleador y por obra social, colocaciones en dólares y canjes del Tesoro.
- **Historia de precios para Dollar Linked y duales**: hoy no se conserva el dólar futuro vigente de cada día, por lo que su TIR histórica no se reconstruye.
- **Indicadores prudenciales que solo el BCRA da en prosa** (por ejemplo PDE y LCR): sin serie estructurada disponible, hoy quedan fuera.
- **Cronogramas de pago desde un endpoint estructurado**: hoy la fuente de los flujos es el prospecto en PDF verificado en capas; se evalúa si MAE o BYMA exponen los cronogramas por API.

## Contacto

¿Preguntas sobre el proyecto o interés en el desarrollo? [Alan Yapaze en LinkedIn](https://www.linkedin.com/in/alan-yapaze/)

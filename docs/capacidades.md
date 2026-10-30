# Todo lo que ofrece el proyecto

El proyecto son **dos sistemas privados que comparten una carpeta de datos** (sin ninguna dependencia de código entre ambos, solo el contrato de carpetas): un **motor de descarga** que llena la carpeta y una **terminal de análisis** que la lee. Esta página los resume completos; cada fila dice dónde verlo.

## 1. Motor de descarga y datos

| Capacidad | Qué hace | Dónde verlo |
|---|---|---|
| **14 fuentes públicas** | BCRA (API y planillas), FRED (~1.100 series curadas más el catálogo de ~800.000), INDEC (26 módulos), UTDT, mercados argentinos (riesgo país, dólares, FCI, tasas), Yahoo Finance (~60 tickers), BCE, BIS, Tesoro de EE.UU., fiscal (datos.gob.ar), ARCA, FMI, Secretaría de Finanzas, y renta fija (MAE e IOL) | [Datos y pipeline](datos_y_pipeline.md) |
| **Descarga incremental y validada** | Solo trae lo nuevo, valida estructura, historia mínima y frescura, escribe de forma atómica y **nunca pisa un archivo bueno con uno incompleto**; manejo propio de límites de cada API | [Datos y pipeline](datos_y_pipeline.md) |
| **INDEC gateado por su calendario** | Un crawler con navegador automatizado (3 instancias en paralelo) que solo va a buscar cada módulo el día que le corresponde: lee el PDF del calendario semestral (~184 fechas) y le suma las fechas reales, que a veces se reprograman. Si no sabe la fecha de algo, lo busca igual: nunca pierde un dato por un mapeo incompleto | [Datos y pipeline](datos_y_pipeline.md) · [Calendarios](capturas/calendarios.md) |
| **Universo de bonos y prospectos** | Indexa el universo completo de títulos públicos, obligaciones negociables y fideicomisos del MAE (no solo los «vigentes» que lista su API), baja los PDF legales de cada emisión y los lee en paralelo, con reconocimiento óptico de caracteres en las páginas escaneadas | [Datos y pipeline](datos_y_pipeline.md) |
| **Fichado de bonos con control** | Cada bono tiene una ficha estructurada (cronograma de pagos, moneda, indexación, ley). Se arma **caso por caso con un protocolo escrito y asistencia de IA**, y un verificador revisa esquema, coherencia moneda-indexación, campos recomendados y fichas sin completar. Un parser automático se **descartó** porque dejó un bono real con 26 cuotas trimestrales al 0 % en vez de sus 42 cuotas mensuales | [Ingeniería y calidad](ingenieria.md) |
| **Precios y puntas en vivo** | Precios y puntas (mejor compra y venta) de bonos, letras y ONs, MEP oficial y futuros de dólar, cada 30 minutos en horario de mercado, con un **medidor de cuota de API** que corta antes de arriesgar el acceso (~24 % del cupo mensual) | [Datos y pipeline](datos_y_pipeline.md) |
| **Índice maestro y salud de los datos** | Un índice de todas las series (fuente, tema, cobertura, estado) y un reporte de salud con umbrales de frescura por frecuencia: distingue una serie **desactualizada** de una **fuente que dejó de publicar**. Un historial de corridas deja constancia de cada descarga | [Datos y pipeline](datos_y_pipeline.md) |
| **Verificación previa a descargar** | Un comando que, sin bajar nada, valida que las series configuradas pertenezcan a su release, que las URLs de las planillas respondan y que el intérprete tenga todas las dependencias | [Datos y pipeline](datos_y_pipeline.md) |
| **Versiones históricas de series revisadas** | **ALFRED** (14 series de la Fed que se revisan: empleo, producción, PIB, condiciones financieras...) y **versiones propias de series argentinas** (EMAE, PIB, IPC, balanza, salarios, agregados monetarios, tipo de cambio real, fiscal): como el INDEC y el BCRA no publican las versiones anteriores, el sistema guarda cada valor y desde qué día estuvo publicado. Sirven para medir «qué se sabía cada día» y para probar sin mirar el futuro | [Datos y pipeline](datos_y_pipeline.md) |
| **6 tareas programadas** | Macro diaria, precios y pricing cada 30 minutos, mercados globales cada 30 minutos, historia y respaldo semanales, alertas diarias y un vigilante que controla que la maquinaria diaria haya corrido | [Datos y pipeline](datos_y_pipeline.md) |
| **Respaldo verificado y datos en disco aparte** | Respaldo semanal con manifiesto SHA-256 de lo que no se puede volver a bajar; los datos viven fuera del repositorio y de la carpeta sincronizada con la nube | [Ingeniería y calidad](ingenieria.md) |

## 2. Terminal de análisis

**153 pantallas en 7 secciones**, cada una con su recorrido completo y la explicación que muestra la propia herramienta:

| Sección | Pantallas | Qué hay |
|---|---|---|
| [🏠 Inicio](capturas/inicio.md) | 1 | El brief de la mañana: banderas de riesgo, cuadro cambiario con brecha, contexto overnight y el reloj de mercados del mundo. |
| [🌊 Liquidez Global](capturas/liquidez.md) | 29 | El contexto global que mueve al mercado: régimen de riesgo, reloj del ciclo de EE.UU., valuación, burbujas, exposición, pronósticos, política de riesgo, spreads, liquidez y curvas. |
| [🇦🇷 Macro Argentina](capturas/macro.md) | 19 | La economía real y financiera del país con datos oficiales: postura monetaria, cambiario, reservas, fiscal, monetario, crédito y bancos, actividad, precios, empleo, consumo, externo y expectativas. |
| [📊 Renta Fija / Bonos](capturas/renta-fija.md) | 24 | Pricing de 7 tipos de instrumento en tres vistas de precio, curvas, valor relativo, arbitraje, carry trade, armador de portafolios y fichas por bono. |
| [📈 Renta Variable](capturas/renta-variable.md) | 5 | Análisis fundamental de empresas a partir de los estados contables presentados ante la SEC. |
| [💼 Mi cartera](capturas/cartera.md) | 15 | Diario de operaciones, tenencias con TIR del dinero, riesgo y estrés, calificaciones, swing en renta fija, fuerza relativa y rotación. |
| [📅 Calendarios](capturas/calendarios.md) | 3 | Cuándo salen los informes del INDEC y del BCRA, y cuándo cobra cada bono. |

### Lo que atraviesa toda la terminal

| Capacidad | Qué hace | Dónde verlo |
|---|---|---|
| **Pricing de 7 tipos de instrumento en 3 vistas** | Tasa fija, CER, Dollar Linked, duales, hard dollar soberano y corporativo, y corporativos en pesos; cada métrica en vista Mercado, Compra y Venta, con comisiones del broker verificadas contra su fuente | [Motores y herramientas](motores.md) |
| **Herramientas de decisión** | Curvas por clase, valor relativo, arbitraje, carry trade, breakeven de retorno total, radar, simulador, armador de portafolios con Markowitz y plan de rebalanceo | [Renta Fija](capturas/renta-fija.md) · [Herramientas clave](capturas/herramientas_clave.md) |
| **Entregables** | Ficha Excel por instrumento con el valor presente de cada flujo, exportación masiva del universo y Research Report en PDF | [Excel y PDF](excel.md) |
| **Mi cartera** | Diario de operaciones, tenencias FIFO, TIR del dinero, riesgo y estrés, calificaciones de la CNV, tesis con condiciones medibles, swing en renta fija, fuerza relativa y reporte semanal en texto | [Mi cartera](capturas/cartera.md) |
| **Cada módulo dice cuánta evidencia tiene** | Todas las lecturas siguen una misma forma: qué dicen, hacia dónde sesgan, con qué grado de evidencia y **qué las cambiaría**. Lo que no está probado se rotula así | [Hallazgos](hallazgos.md) |
| **Mesa de control y rutas guiadas** | En Inicio, las seis preguntas del analista (el mundo, el ciclo, si conviene estar posicionado, Argentina, el mercado local y la cartera) con una lectura cada una, y la escalera de horizontes | [Inicio](capturas/inicio.md) |
| **Buscador, glosario y diccionario de indicadores** | Salta a cualquier pantalla por nombre, tema o indicador; define los términos técnicos con un ejemplo de cómo se leen; y una ficha por indicador con su definición, fuente, frecuencia, rezago, evidencia y documento | [Inicio](capturas/inicio.md) |
| **Alertas y vigilancia** | Avisos por **cambio de lectura** del sistema, avisos de cambios en las fuentes del INDEC (archivos renombrados, nuevos o que desaparecen), un vigilante que controla las tareas diarias, un historial de lecturas por fecha y una repetición histórica de las alertas para ver **si habrían servido** | [Ingeniería y calidad](ingenieria.md) |
| **Registro de predicciones** | Cada lectura queda anotada con su fecha y se evalúa después, solo con lo que ya ocurrió; lo que llega tarde (valuación, burbujas) se dice en pantalla | [Pronósticos](capturas/liquidez.md) |
| **Pruebas con método** | Backtests del ciclo de EE.UU. y de Argentina, burbujas, coincidencias de valor, curvas de rendimiento, posicionamiento, régimen de riesgo, valuación y versiones históricas; robustez «sacando un episodio»; pruebas escritas **antes** de mirar los datos (nowcast de actividad, riesgo país local, corridas, forwards), con los resultados negativos publicados | [Hallazgos](hallazgos.md) |
| **Calidad de datos en cada capa** | Un chequeo automático de los archivos derivados (claves duplicadas, fechas mal cargadas, huecos, datos viejos), frescura de 23 módulos del INDEC contra su calendario real y un panel de salud de datos dentro de la terminal | [Ingeniería y calidad](ingenieria.md) |
| **Auditoría de cálculos** | 11 oleadas en las que cada módulo se leyó, se recalculó a mano con datos reales y se corrigió con un test de regresión | [Ingeniería y calidad](ingenieria.md) |

**En números:** más de 120 módulos de cálculo, 30 parsers de planillas del INDEC, 2.704 tests automatizados y 6 tareas programadas.

---
[← Volver al inicio del repositorio](../README.md)

# Ingeniería y control de calidad

## Auditoría de cálculos en 11 oleadas (29-30 de octubre de 2026)

Método de cada oleada: leer el código de un grupo de módulos, **recalcular a mano con datos reales** lo que muestran, probar los casos límite, corregir con un **test de regresión**, verificar la pantalla en el navegador y documentar. Un cambio solo entra si la suite completa pasa (2.704 tests). Lo que se encontró, con su impacto:

| Hallazgo | Impacto | Cómo se resolvió |
|---|---|---|
| **«Mejor rezago» de una correlación cruzada** comparado con la banda de un solo rezago | Con series persistentes e independientes marcaba «significativo» en 37 de cada 40 pares | Se calibra contra el azar rotando una serie en círculo: 1 de cada 40; las pantallas dicen cuándo el mejor rezago no se distingue del azar |
| **Variaciones interanuales calculadas «por filas»** sobre series con meses sin publicar | En shoppings, una serie con un hueco de 8 meses en 2020 mostraba +219 % y −96 % donde correspondía «sin dato» | Comparación contra el mismo mes de calendario en 10 módulos; los cambios «en 12 meses» de las tarjetas también |
| **TIR agregada de la cartera** con una posición en dólares sin cronograma | La TIR de mercado pasaba de 10,1 % a −7,6 % | El valor, el costo y los flujos salen del mismo conjunto de bonos |
| **Parser de PDF del informe de bancos** que dejaba vacíos indicadores por cambios de redacción | ROA a 12 meses leído en 4 de 9 informes; transferencias inmediatas en 0 de 9 | Reglas nuevas verificadas contra el texto de cada informe; lo que no se lee queda vacío en vez de equivocado |
| **Ventana en meses leída como ruedas** en el registro de predicciones | Una lectura de valuación (retorno a 5 años) se daba por evaluable a los 3 meses | Ventana de 1.260 ruedas y test |
| **Textos fijos que contradecían la tabla de al lado** | El reloj decía que tras «desaceleración» una recesión llegó «una de cada tres» veces; la tabla mostraba 64 % | Las frases se generan de los números |
| **IPC intervenido del INDEC (2007-octubre 2015)** dentro de comparaciones | Inflación «sin poder predictivo» por usar un dato subestimado; recaudación y haber mínimo reales sobreestimados | Medidas solo desde noviembre de 2015 y avisos donde el tramo se cruza |
| **Selección de indicadores con la misma historia con que se mide** | Tablas del reloj del ciclo y de las reglas de exposición optimistas | Aviso «en la muestra», prueba «sacando cada recesión» y puntaje solo con los robustos al lado |
| **Bandas del REM aplicadas desde el cierre de la encuesta** | Mirada al futuro de unos 6 días respecto de la publicación | Rige la encuesta anterior hasta que se publica la nueva |
| **Habilidad del REM promediada con el plazo 0** (donde casi siempre gana) | Ocultaba que a 3-6 meses el REM no mejoraba a extrapolar en 2017-2019 | Se mide en el mismo tramo que el sesgo y contra dos referencias ingenuas |
| **Patrón de expresión regular corrupto** (un carácter de control en vez de `\b`) | Bonos Par y Descuento clasificados en la familia equivocada | Reglas por nombre del instrumento con test |
| **Rezago del CER contra el REM, sensibilidades y escalas** de la ficha, forwards y breakeven | Diferencias de uno a pocos puntos básicos entre pantallas | Una sola implementación y tests de paridad |

**Pruebas pre-registradas y resultados negativos:** el nowcast del EMAE, el riesgo país local y la utilidad de los forwards se probaron con la regla escrita antes de mirar, y los resultados negativos están publicados.

**Control de frescura:** 23 módulos del INDEC comparados contra su calendario real de difusión, tareas programadas con alertas, y datos en un disco aparte con respaldo verificado (manifiesto SHA-256).


## Hallazgos de auditorías anteriores

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
| **Control de calidad** | 2.704 tests, contrastes contra fuentes independientes (FMI, informes oficiales), auditorías documentadas con hallazgos reales y corregidos |
| **Producto** | Una terminal con 7 secciones y 153 pantallas y vistas pensadas para un analista: qué cambió hoy, alertas, fichas por instrumento, exportación a Excel de nivel research desk |
| **Criterio** | Cada número se puede defender, cada límite se declara en pantalla y el sistema prefiere mostrar "sin dato" a una cifra fabricada |

**Cómo trabajo con IA:** la lógica financiera, las decisiones de diseño y la verificación son propias; la implementación de código se desarrolló con asistentes de IA (Claude, Gemini). El valor está en dirigirlos con criterio y en el proceso de verificación (tests, contrastes y auditorías) que impide aceptar un resultado sin comprobarlo.

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
| Validación | pytest (2.704 tests: la suite del motor de pricing y el dashboard — reconciliación PVCF↔precio del exportador Excel, invariantes por familia de instrumento, controles cruzados contra fuentes independientes, paridad entre la tarea programada y la corrida manual, funciones puras 100% sintéticas — y 180 sobre el motor de descarga con sesiones simuladas sin red) + GitHub Actions (CI en cada push: compileall + pytest) |
| Automatización | Windows Task Scheduler (4 tareas: macro diaria; precios, pricing y export cada 30 min en horario de mercado; mercados globales cada 30 min; historia, respaldo y calidad semanales) con control de cuota de API |

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

---
[← Volver al inicio del repositorio](../README.md)

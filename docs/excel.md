# Exportación a Excel y PDF

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

## Ejemplos de fichas exportadas

Cada ficha trae, en una sola hoja: métricas de sensibilidad, las tres vistas de precio, el detalle del prospecto, el cronograma con el valor presente de cada flujo, el análisis de sensibilidad con su gráfico y los comparables. El pie de cada hoja indica la fecha y hora de generación.

**Hard Dollar (soberanos, provinciales y ONs)** — ejemplo: el **GD46** (soberano ley Nueva York, amortizable en 40 pagos). Cronograma en USD, con el control que reconcilia la suma de los PVCF contra el precio pagado.

<p align="center"><img src="../assets/screenshots/excel_ficha_hard_dollar_GD46_metricas_vistas.webp" alt="Ficha Excel GD46: métricas financieras y vistas de precio Mercado / Compra / Venta con la tarifa aplicada" width="100%"><br><sub>Ficha Excel GD46: métricas financieras y vistas de precio Mercado / Compra / Venta con la tarifa aplicada</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_hard_dollar_GD46_riesgo_contexto.webp" alt="Ficha Excel GD46: riesgo, ejecución y contexto de la TIR (bid/ask, fricción, volumen, MEP/CCL implícito, breakeven y percentil histórico)" width="100%"><br><sub>Ficha Excel GD46: riesgo, ejecución y contexto de la TIR (bid/ask, fricción, volumen, MEP/CCL implícito, breakeven y percentil histórico)</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_hard_dollar_GD46_prospecto_cronograma.webp" alt="Ficha Excel GD46: detalle del prospecto y cronograma de 40 pagos con PVCF, flujos netos de la vista Compra y fila de totales" width="100%"><br><sub>Ficha Excel GD46: detalle del prospecto y cronograma de 40 pagos con PVCF, flujos netos de la vista Compra y fila de totales</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_hard_dollar_GD46_sensibilidad.webp" alt="Ficha Excel GD46: análisis de sensibilidad del precio a la TIR (±10 pp) con su gráfico" width="100%"><br><sub>Ficha Excel GD46: análisis de sensibilidad del precio a la TIR (±10 pp) con su gráfico</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_hard_dollar_GD46_comparables_repago.webp" alt="Ficha Excel GD46: comparables por duration y perfil de repago" width="100%"><br><sub>Ficha Excel GD46: comparables por duration y perfil de repago</sub></p>

**CER** — cronograma en **pesos nominales**: cada flujo usa el coeficiente CER proyectado a su propia fecha de pago, así un cupón contractualmente parejo se ve crecer en pesos; la TIR y la sensibilidad siguen en términos reales.

<p align="center"><img src="../assets/screenshots/excel_ficha_cer_TX28_metricas_vistas.webp" alt="Ficha Excel CER (TX28): métricas y vistas" width="100%"><br><sub>Ficha Excel CER (TX28): métricas y vistas</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_cer_TX28_prospecto_cronograma.webp" alt="Ficha Excel CER (TX28): cronograma nominal con coeficiente CER" width="100%"><br><sub>Ficha Excel CER (TX28): cronograma nominal con coeficiente CER</sub></p>

**Tasa Fija / LECAP / BONCAP** — flujos a tasa fija en pesos, con TEA, TNA y TEM y las tres vistas de precio.

<p align="center"><img src="../assets/screenshots/excel_ficha_tasa_fija_S30O6_metricas_vistas.webp" alt="Ficha Excel Tasa Fija (S30O6): métricas y vistas" width="100%"><br><sub>Ficha Excel Tasa Fija (S30O6): métricas y vistas</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_tasa_fija_S30O6_prospecto_cronograma.webp" alt="Ficha Excel Tasa Fija (S30O6): cronograma" width="100%"><br><sub>Ficha Excel Tasa Fija (S30O6): cronograma</sub></p>

**Dollar Linked** — cada flujo en USD y en ARS al tipo de cambio proyectado de ese día bajo cada uno de los cuatro escenarios (REM, ROFEX, A3500 y Banda).

<p align="center"><img src="../assets/screenshots/excel_ficha_dollar_linked_TZV27_metricas_vistas.webp" alt="Ficha Excel Dollar Linked (TZV27): métricas y escenarios" width="100%"><br><sub>Ficha Excel Dollar Linked (TZV27): métricas y escenarios</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_dollar_linked_TZV27_prospecto_cronograma.webp" alt="Ficha Excel Dollar Linked (TZV27): cronograma" width="100%"><br><sub>Ficha Excel Dollar Linked (TZV27): cronograma</sub></p>

**Corporativos y provinciales en ARS, y Duales** — mismas secciones, con el ruteo por pata ganadora en los duales.

<p align="center"><img src="../assets/screenshots/excel_ficha_corporativo_ars_RVS1O_metricas_vistas.webp" alt="Ficha Excel corporativo ARS (RVS1O): métricas y vistas" width="100%"><br><sub>Ficha Excel corporativo ARS (RVS1O): métricas y vistas</sub></p>

<p align="center"><img src="../assets/screenshots/excel_ficha_corporativo_ars_RVS1O_prospecto_cronograma.webp" alt="Ficha Excel corporativo ARS (RVS1O): cronograma" width="100%"><br><sub>Ficha Excel corporativo ARS (RVS1O): cronograma</sub></p>

**Archivo "Global" por clase** — hoja *Resumen* con hipervínculos a la hoja de cada ticker, escala de color condicional en TIR y paridad, y la curva de rendimiento por grupo de emisor.

<p align="center"><img src="../assets/screenshots/excel_global_hard_dollar_resumen.webp" alt="Excel Global Hard Dollar: hoja Resumen con escala de color y vínculos a cada ficha" width="100%"><br><sub>Excel Global Hard Dollar: hoja Resumen con escala de color y vínculos a cada ficha</sub></p>

<p align="center"><img src="../assets/screenshots/excel_global_tasa_fija_resumen.webp" alt="Excel Global Tasa Fija: hoja Resumen" width="100%"><br><sub>Excel Global Tasa Fija: hoja Resumen</sub></p>

<p align="center"><img src="../assets/screenshots/excel_global_cer_resumen.webp" alt="Excel Global CER: hoja Resumen" width="100%"><br><sub>Excel Global CER: hoja Resumen</sub></p>

**Research Report en PDF** — el reporte para enviar a un cliente o inversor: resumen ejecutivo con las banderas de riesgo, contexto macro y cambiario, las curvas de rendimiento y las métricas tácticas. Usa los mismos gráficos y cálculos que ve el analista en pantalla.

<p align="center"><img src="../assets/screenshots/pdf_research_report_1_resumen_ejecutivo.webp" alt="Research Report en PDF: resumen ejecutivo con banderas de riesgo" width="100%"><br><sub>Research Report en PDF: resumen ejecutivo con banderas de riesgo</sub></p>

<p align="center"><img src="../assets/screenshots/pdf_research_report_2_curva_tasa_fija_y_metricas.webp" alt="Research Report en PDF: curva de rendimiento de Tasa Fija y métricas" width="100%"><br><sub>Research Report en PDF: curva de rendimiento de Tasa Fija y métricas</sub></p>

<p align="center"><img src="../assets/screenshots/pdf_research_report_3_metricas_hard_dollar_soberanos.webp" alt="Research Report en PDF: métricas de soberanos y provinciales en dólares" width="100%"><br><sub>Research Report en PDF: métricas de soberanos y provinciales en dólares</sub></p>

<p align="center"><img src="../assets/screenshots/pdf_research_report_4_matriz_de_breakeven.webp" alt="Research Report en PDF: matriz de breakeven" width="100%"><br><sub>Research Report en PDF: matriz de breakeven</sub></p>

---
[← Volver al inicio del repositorio](../README.md)

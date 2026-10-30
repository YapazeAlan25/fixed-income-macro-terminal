# Herramientas clave, paso a paso

> ℹ️ **Sobre estas capturas:** muestran una herramienta de análisis con datos de mercado del día. Los tickers, rendimientos y señales que aparecen son resultados del cálculo para ilustrar su funcionamiento: **no son recomendaciones de inversión** ni ofertas de compra o venta.

Pantallas con parámetros elegidos (capturas del 14-15/10/2026 con el modelo vigente): el recorrido completo del Armador de portafolios con optimización de Markowitz, la matriz de sensibilidad (TIR breakeven), la ficha de bono y las comisiones.

## Comisiones del broker y vistas Mercado / Compra / Venta
Selector global de **vista de precios** (*Mercado* = último precio operado, sin comisión; *Compra* = precio neto de comprar, con comisión e IVA; *Venta* = lo que se cobra al vender) y panel de **comisiones del broker**: tarifas por clase de activo con su fuente y vigencia, IVA y derechos de mercado editables, y recálculo del pricing con el perfil elegido. Ver Comisiones y vistas Mercado / Compra / Venta.

<p align="center"><img src="../../assets/screenshots/rf_sidebar_filtros_globales_vista_de_precios.webp" alt="Vista de precios Mercado / Compra / Venta en la barra lateral" width="100%"><br><sub>Vista de precios Mercado / Compra / Venta en la barra lateral</sub></p>

<p align="center"><img src="../../assets/screenshots/rf_panel_comisiones_del_broker.webp" alt="Panel de comisiones del broker: tarifas por clase, IVA y derechos de mercado editables" width="100%"><br><sub>Panel de comisiones del broker: tarifas por clase, IVA y derechos de mercado editables</sub></p>


## Matriz de Sensibilidad: TIR Breakeven (Total Return)
Para cada instrumento de una categoría (Hard Dollar, CER, Tasa Fija, Dollar Linked, Corporativos ARS, Duales), calcula el retorno total de comprar hoy y vender en una fecha objetivo elegida, contra una grilla de TIRs de salida hipotéticas, con 3 estrategias de reinversión de los cupones cobrados en el camino (caja al 0%, tasa de mercado dinámica, o reinversión a la TIR del propio bono) — y una columna **BREAKEVEN**: la TIR de salida máxima que el instrumento puede tolerar antes de que la posición pase a pérdida nominal, más una columna **Margen** (Breakeven − TIR de hoy, en puntos porcentuales) para leer de un vistazo qué tan ajustada está cada posición. Un resumen agregado arriba de la tabla (bonos totales, margen promedio, cuántos con margen bajo o negativo) evita tener que leer fila por fila para notar que una categoría entera quedó apretada, y una alerta equivalente aparece directo en el brief matutino para los Soberanos Hard Dollar.

Cada categoría respeta su propia unidad de tasa en vez de forzar una sola convención: para CER, la TIR de salida es **real** (post-inflación) — los flujos remanentes se descuentan en base real (VN100, sin CER) y el precio de salida se reinfla una única vez con el coeficiente CER proyectado a la fecha de salida elegida, no con el de cada cupón individual (que ya lleva la inflación de esa fecha puntual incorporada) — evitando el error clásico de contar la inflación proyectada dos veces. Para Dollar Linked se puede elegir el escenario de FX de referencia (REM/ROFEX/A3500/Banda). Para la estrategia de reinversión "tasa de mercado", el input se pide como TNA (como cotiza una caución real) y se convierte a TEA antes de capitalizar — capitalizarla directo como si ya fuera una TEA anual (un error real, encontrado y corregido en la propia auditoría del proyecto) subestima cuánto rinde una caución que se renueva cada pocos días, no una vez al año.

Los bonos Duales entran a la matriz con la pata (TAMAR/CER/Fija/Dollar Linked) que efectivamente paga más **en cada fecha de pago** — no la que gana hoy asumida fija para siempre — reusando el mismo cronograma que ya resuelve la TIR del instrumento internamente. Cada ficha de bono individual, además, muestra un mini-Breakeven directo (+3/+6/+12 meses) sin tener que ir a buscarlo a esta pestaña, y toda la matriz se puede exportar a Excel con los valores numéricos reales (no el texto ya formateado de pantalla), con escala de color condicional.

<p align="center"><img src="../../assets/screenshots/breakeven_1_supuestos_y_resumen.webp" alt="Matriz de sensibilidad: supuestos y resumen de riesgo agregado" width="100%"><br><sub>Matriz de sensibilidad: supuestos y resumen de riesgo agregado</sub></p>

<p align="center"><img src="../../assets/screenshots/breakeven_2_perfil_retorno_total_soberanos_ley_local_real.webp" alt="Perfil de retorno total en términos reales: soberanos ley local (AL29, AL30, AL35, AL41) y AO28 / AO29" width="100%"><br><sub>Perfil de retorno total en términos reales: soberanos ley local (AL29, AL30, AL35, AL41) y AO28 / AO29</sub></p>

<p align="center"><img src="../../assets/screenshots/breakeven_3_matriz_tir_breakeven_ordenada_por_bono.webp" alt="Matriz de TIR breakeven por bono y TIR de salida (retorno nominal y real)" width="100%"><br><sub>Matriz de TIR breakeven por bono y TIR de salida (retorno nominal y real)</sub></p>


## Armador con optimización de Markowitz — cartera de 9 bonos con topes por emisor y por sector
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

<p align="center"><img src="../../assets/screenshots/armador_markowitz_1_configuracion.webp" alt="Configuración: máximo Sharpe, volumen mínimo 100.000, USD 10.000, 6 a 10 bonos, tope 25 % por emisor y 30 % por sector" width="100%"><br><sub>Configuración: máximo Sharpe, volumen mínimo 100.000, USD 10.000, 6 a 10 bonos, tope 25 % por emisor y 30 % por sector</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_2_composicion_sugerida.webp" alt="Composición sugerida con peso, monto, nominales, precio neto de compra, TIR y duration" width="100%"><br><sub>Composición sugerida con peso, monto, nominales, precio neto de compra, TIR y duration</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_3_metricas_de_la_cartera.webp" alt="Métricas de la cartera: TIR y duration rigurosas, costo de implementación y TIR de ida y vuelta" width="100%"><br><sub>Métricas de la cartera: TIR y duration rigurosas, costo de implementación y TIR de ida y vuelta</sub></p>

<details>
<summary>📷 Ver las 7 capturas restantes de esta sección</summary>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_4_calendario_de_flujos.webp" alt="Calendario de flujos proyectado por bono y recupero acumulado" width="100%"><br><sub>Calendario de flujos proyectado por bono y recupero acumulado</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_5_por_que_esta_combinacion.webp" alt="Por qué esta combinación: objetivo, desvío, VaR y concentración" width="100%"><br><sub>Por qué esta combinación: objetivo, desvío, VaR y concentración</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_6_estres_de_tasa.webp" alt="Estrés de tasa con re-descuento completo" width="100%"><br><sub>Estrés de tasa con re-descuento completo</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_7_correlacion.webp" alt="Correlación entre los bonos sugeridos" width="100%"><br><sub>Correlación entre los bonos sugeridos</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_8_frontera_eficiente.webp" alt="Frontera eficiente" width="100%"><br><sub>Frontera eficiente</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_9_backtest_historico.webp" alt="Backtest histórico retroactivo de la cartera" width="100%"><br><sub>Backtest histórico retroactivo de la cartera</sub></p>

<p align="center"><img src="../../assets/screenshots/armador_markowitz_10_metricas_de_riesgo.webp" alt="Métricas de riesgo sobre la serie del backtest (VaR, CVaR, Sortino)" width="100%"><br><sub>Métricas de riesgo sobre la serie del backtest (VaR, CVaR, Sortino)</sub></p>

</details>


## Ficha de bono: drill-down completo por instrumento
Click en cualquier ticker de las tablas de pricing de arriba abre una vista de detalle dedicada
para ese instrumento: métricas de sensibilidad completas (TIR, Paridad, Duration, **Vida
Promedio** —distinta de Duration: pesa solo la devolución de capital sin descontar, el número
que de verdad importa en bonos que amortizan en cuotas, no al vencimiento entero—, Convexidad,
DV01 en dos escalas —por 100 de nominal y por una posición real de VN 1.000.000—, spread contra
la curva propia del emisor y concentración del emisor en el universo), detalle completo del
prospecto, cronograma de flujos con Valor Presente de cada flujo (misma estructura que un
modelo de manual de finanzas), gráfico de sensibilidad Precio-vs-TIR con la aproximación
clásica de Duration-Convexidad superpuesta contra el reprecio exacto, un **mini-Breakeven a
+3/+6/+12 meses** (misma lógica que la Matriz de Breakeven completa, ver Capturas,
sin tener que salir de la ficha para una primera lectura del margen de seguridad), comparables
por duration dentro de la misma clase de emisor, otros instrumentos del mismo emisor exacto, y
un botón para exportar la misma ficha, completa, a Excel.

<p align="center"><img src="../../assets/screenshots/ficha_hard_dollar_AL30_kpis_riesgo_vistas.webp" alt="Ficha AL30: KPIs, riesgo crediticio y vistas de precio Mercado / Compra / Venta" width="100%"><br><sub>Ficha AL30: KPIs, riesgo crediticio y vistas de precio Mercado / Compra / Venta</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_hard_dollar_AL30_prospecto_cronograma.webp" alt="Ficha AL30: detalle del prospecto y cronograma de flujos con PVCF y fila de totales" width="100%"><br><sub>Ficha AL30: detalle del prospecto y cronograma de flujos con PVCF y fila de totales</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_hard_dollar_AL30_breakeven_comparables.webp" alt="Ficha AL30: mini-breakeven a 3, 6 y 12 meses y comparables" width="100%"><br><sub>Ficha AL30: mini-breakeven a 3, 6 y 12 meses y comparables</sub></p>

<details>
<summary>📷 Ver las 11 capturas restantes de esta sección</summary>

<p align="center"><img src="../../assets/screenshots/ficha_cer_TX28_kpis_riesgo_vistas.webp" alt="Ficha CER (TX28): TIR real, coeficiente CER y vistas de precio" width="100%"><br><sub>Ficha CER (TX28): TIR real, coeficiente CER y vistas de precio</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_cer_TX28_prospecto_cronograma.webp" alt="Ficha CER (TX28): cronograma en pesos nominales con coeficiente CER proyectado" width="100%"><br><sub>Ficha CER (TX28): cronograma en pesos nominales con coeficiente CER proyectado</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_cer_TX28_breakeven_comparables.webp" alt="Ficha CER (TX28): mini-breakeven y comparables" width="100%"><br><sub>Ficha CER (TX28): mini-breakeven y comparables</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_tasa_fija_S30O6_kpis_riesgo_vistas.webp" alt="Ficha Tasa Fija (S30O6): TEA, TNA y vistas de precio" width="100%"><br><sub>Ficha Tasa Fija (S30O6): TEA, TNA y vistas de precio</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_tasa_fija_S30O6_prospecto_cronograma.webp" alt="Ficha Tasa Fija (S30O6): prospecto y cronograma" width="100%"><br><sub>Ficha Tasa Fija (S30O6): prospecto y cronograma</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_tasa_fija_S30O6_breakeven_comparables.webp" alt="Ficha Tasa Fija (S30O6): mini-breakeven y comparables" width="100%"><br><sub>Ficha Tasa Fija (S30O6): mini-breakeven y comparables</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_dollar_linked_TZV27_kpis_vistas.webp" alt="Ficha Dollar Linked (TZV27): escenario de dólar, KPIs y vistas de precio" width="100%"><br><sub>Ficha Dollar Linked (TZV27): escenario de dólar, KPIs y vistas de precio</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_dollar_linked_TZV27_cronograma.webp" alt="Ficha Dollar Linked (TZV27): cronograma en USD y ARS" width="100%"><br><sub>Ficha Dollar Linked (TZV27): cronograma en USD y ARS</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_corporativo_ars_RVS1O_kpis_riesgo_vistas.webp" alt="Ficha corporativo en pesos (RVS1O): KPIs y vistas de precio" width="100%"><br><sub>Ficha corporativo en pesos (RVS1O): KPIs y vistas de precio</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_corporativo_ars_RVS1O_prospecto_cronograma.webp" alt="Ficha corporativo en pesos (RVS1O): prospecto y cronograma" width="100%"><br><sub>Ficha corporativo en pesos (RVS1O): prospecto y cronograma</sub></p>

<p align="center"><img src="../../assets/screenshots/ficha_corporativo_ars_RVS1O_breakeven_comparables.webp" alt="Ficha corporativo en pesos (RVS1O): mini-breakeven y comparables" width="100%"><br><sub>Ficha corporativo en pesos (RVS1O): mini-breakeven y comparables</sub></p>

</details>


# Motores y herramientas

Los motores de cálculo y las herramientas del terminal. Para verlos en pantalla: [capturas por sección](capturas/README.md) y [herramientas clave](capturas/herramientas_clave.md).

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

Las capturas de una corrida real (USD 10.000, volumen mínimo 100.000, tope 25 % por emisor y 30 % por sector, 6 a 10 bonos) están en Armador con optimización de Markowitz.

## Terminal de analista: valor relativo, TIR histórica y señales

- **TIR histórica diaria reconstruida con los pricers reales**: cada precio pasado se valúa con la misma matemática del dashboard en vivo y solo con lo que se sabía ese día (CER publicado, REM vigente, TAMAR hasta el día anterior). Contrastada: con los precios de hoy reproduce 58 de 58 TIR del pricer vivo con diferencia de 0,00 pb. Incluye 101 instrumentos en pesos ya vencidos para evitar el **sesgo de supervivencia**.
- **Curva de rendimiento elegida con evidencia**: logarítmica por defecto (Nelson-Siegel solo en Dollar Linked), con mínimo de 5 bonos y calidad de ajuste visible en cada panel; el backtest es reproducible (script propio del proyecto).
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
- **Menos PDF, más datos**: se reemplazó la lectura de PDF por fuentes estructuradas siempre que existen. El contraste contra el PDF anterior encontró dos errores del parser viejo (ver Rigor de ingeniería).

## Mi cartera: tenencias, riesgo y calificaciones

El seguimiento de una cartera propia con el mismo rigor que el pricing:

- **Diario de operaciones** con precio neto de comisión (real o del modelo, marcando el origen) y **TIR de compra con el pricer real** a la fecha de cada operación.
- **Tenencias por FIFO**: valor de liquidación (neto de comisión), cobros asignados al lote que los recibió, **TIR del dinero** (una sola tasa que iguala lo pagado en cada compra, con su fecha) y XIRR; cada compra se descompone en carry, cambio de tasa y comisiones.
- **Riesgo y estrés**: descomposición de Euler del riesgo por posición y por factor (inflación, dólar, soberano USD, crédito, tasa en pesos), escenarios de curva con **reprecio exacto** flujo por flujo, VaR y ES, y estrés histórico con los episodios reales del riesgo país (PASO 2019, COVID, julio 2022, balotaje 2023...).
- **Calificaciones**: lee de la CNV los dictámenes de las agencias locales (FIX, Moody's Local, S&P Argentina), normaliza las tres notaciones a una escala común y detecta cambios. La escala internacional va en una tabla manual: esos sitios exigen registro y el sistema **no los esquiva**.
- **Swing trading en renta fija**: hipótesis **definidas antes de correrlas**, con corrección por pruebas múltiples y resultados negativos publicados (una de las tres hipótesis iniciales pierde dinero en todas las configuraciones y se descartó). El monitor **avisa, no opera**.
- **Optimizador** de pesos objetivo (carry menos aversión por varianza menos costo de llegar, con covarianza Ledoit-Wolf), **tesis por operación** con condiciones medibles evaluadas todos los días y alertas.

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

---
[← Volver al inicio del repositorio](../README.md)

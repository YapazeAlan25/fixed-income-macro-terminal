# Novedades

La versión anterior de esta vidriera (15/10/2026) documentaba el pricer, el escritorio macro y Mi cartera. Desde entonces el sistema sumó dos secciones nuevas, más de veinte pantallas y una auditoría completa de los cálculos.

## Secciones nuevas

- **📈 Renta Variable.** Análisis fundamental de empresas a partir de los estados contables presentados ante la SEC (más de 300 emisores): resumen, ficha de empresa, screener, salud agregada del universo y una pestaña de notas y límites. Sin valuación ni precios inventados: solo lo que dicen los balances.
- **📅 Calendarios.** Cuándo salen los informes del INDEC (plan semestral en PDF más las fechas reales de publicación, que a veces se reprograman), cuándo se espera cada publicación del BCRA (estimada por su última publicación observada, y dicho así en pantalla) y **cuándo cobra cada bono**: una grilla por mes y día con renta, amortización o vencimiento, y cuánto se cobra por cada 100 nominales o por una posición.

## Macro Argentina

- **Postura monetaria**: ¿contractiva, expansiva o acomodaticia? Combina el crecimiento real del dinero en pesos contra la actividad y la tasa real ex-post, en nueve estados, con una prueba de robustez (la etiqueta se mantiene en el 63 % de los meses si se mueven los umbrales un 40 %).
- **Balance cambiario del BCRA** (oferta y demanda de divisas por sector), **corridas y dolarización**, **riesgo local vs global**, **dólar a precios constantes** (por presidente, con el IPC intervenido de 2007-2015 señalado), **dólar medido en salarios, jubilación mínima y canasta**.
- **Salud económica** y **ciclo de actividad** (qué anticipó las contracciones del EMAE: solo las reservas brutas pasan la regla), **nowcast de actividad** (resultado negativo publicado), **precisión del REM** (sesgo y error por plazo, contra dos referencias ingenuas) y **dinero e inflación** (velocidad, exceso de dinero, Granger con controles).
- **Crédito y bancos**: crédito por línea, crecimiento real, costo del crédito, morosidad por segmento, spreads de crédito, **bancos por entidad** (balance, solvencia y calidad de cartera de cada banco, comparados con su grupo homogéneo) y **fondos comunes de inversión**.
- **Entre dos fechas**: qué pasó con inflación, dólar, salarios, dinero y mercados en cualquier período, con la variación real contra el IPC.

## Liquidez Global

- **Régimen de riesgo** (miedo y codicia con 20 indicadores agrupados por tema), **reloj del ciclo** de EE.UU. (qué variables anticiparon las 8 recesiones desde 1969, con puntaje líder y fase), **valuación** (CAPE de Shiller y retorno esperado), **burbujas y exceso**, **¿posicionado o no?** (reglas de exposición contra comprar y mantener, con costos), **pronósticos** con su registro de predicciones, **política de riesgo** y **spreads de crédito**.
- **Explorador de series**: más de mil series curadas de FRED y del BCRA, con los datos de la Fed (H.8) y de la curva de calidad crediticia.

## Renta Fija

- **Bloques de trabajo**: pulso del día, mercado por clase, dónde hay valor, decidir y ejecutar.
- **Escenarios y sensibilidad conjunta** (riesgo país × tasa de EE.UU.) para los bonos en dólares, **atribución de la TIR** (carry, cambio de tasa y comisión), **precios dudosos** marcados, **prima en pesos**, **confianza del dato** por instrumento, curva por ley, prueba de si los forwards predicen y **calendario de cobros**.
- **Calificaciones de la CNV** en las tablas (155 emisores leídos), con escala común entre agencias.

## Mi cartera

Tesis de cada compra con condiciones medibles, calificaciones, riesgo de crédito y de liquidez, estrés histórico con episodios reales del riesgo país, plan de reinversión de cupones, simulador de «¿y si compro o vendo X?» y optimizador con tus propias reglas.

## Calidad

- **Auditoría de cálculos en 11 oleadas** (29-30 de octubre de 2026): cada módulo se leyó, se recalculó a mano con datos reales, se corrigió con un test de regresión y se documentó. Detalle en [Ingeniería y calidad](ingenieria.md).
- **Convención oficial de rendimientos** (TIREA en D30/360 sobre 360, como el Tesoro) en una sola implementación.
- **Frescura de datos con control propio** (23 módulos del INDEC al día, tareas programadas con alertas) y **registro de predicciones** para evaluar al propio sistema con lo que pase después.
- **2.704 tests automatizados.**

---
[← Volver al inicio del repositorio](../README.md)

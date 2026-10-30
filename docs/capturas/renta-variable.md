# 📈 Renta Variable

Análisis fundamental de empresas a partir de los estados contables presentados ante la SEC.

**5 pantallas.** [Volver al índice](README.md) · [Inicio del repositorio](../../README.md)

> ℹ️ **Sobre estas capturas:** muestran una herramienta de análisis con datos de mercado del día. Los tickers, rendimientos y señales que aparecen son resultados del cálculo para ilustrar su funcionamiento: **no son recomendaciones de inversión** ni ofertas de compra o venta.

## Resumen

<img src="../../assets/screenshots/renta-variable_resumen.webp" alt="Resumen" width="100%">

> Fundamentales auditados de las empresas del universo de CEDEARs (estados contables de la SEC) · información descriptiva, sin precios ni valuación · datos al 10/09/2026 El motor de ratios es el de Equity_Analysis, portado sin cambios de lógica; da exactamente los mismos resultados en las 326 empresas (anual y TTM): python tools/paridad_equity.py. La diferencia conocida está en «Notas y límites».

## Ficha de empresa

<img src="../../assets/screenshots/renta-variable_ficha_de_empresa.webp" alt="Ficha de empresa" width="100%">

> Fundamentales auditados de las empresas del universo de CEDEARs (estados contables de la SEC) · información descriptiva, sin precios ni valuación · datos al 10/09/2026 Technology · Nasdaq · CIK 0000320193 · CEDEAR AAPL (ratio 20:1)

## Screener

<img src="../../assets/screenshots/renta-variable_screener.webp" alt="Screener" width="100%">

> Fundamentales auditados de las empresas del universo de CEDEARs (estados contables de la SEC) · información descriptiva, sin precios ni valuación · datos al 10/09/2026 El valor es el del período más reciente que tiene dato para cada empresa (puede ser un año fiscal distinto entre empresas). El percentil es dentro de su sector. Un valor extremo no es una señal de compra o venta: mirá la ficha, el sector, el tamaño y si el ratio aplica a ese negocio. En bancos y aseguradoras varias métricas no aplican (margen bruto, rotación de inventario, liquidez corriente): figuran como N/D.

## Salud agregada

<img src="../../assets/screenshots/renta-variable_salud_agregada.webp" alt="Salud agregada" width="100%">

> Fundamentales auditados de las empresas del universo de CEDEARs (estados contables de la SEC) · información descriptiva, sin precios ni valuación · datos al 10/09/2026 Lo que reportaron ~320 empresas, agregado por año fiscal, para contrastarlo con lo que dicen los mercados de crédito (spread Baa contra el Tesoro) y el ciclo. Años fiscales, no calendario: el último año puede estar incompleto. No hay datos posteriores a la fecha de bajada.

## Notas y límites

<img src="../../assets/screenshots/renta-variable_notas_y_limites.webp" alt="Notas y límites" width="100%">

> Fundamentales auditados de las empresas del universo de CEDEARs (estados contables de la SEC) · información descriptiva, sin precios ni valuación · datos al 10/09/2026 Los datos tienen 50 días: no incluyen los balances publicados desde entonces (en plena temporada de resultados eso es mucho). Se actualizan con el descargador de la SEC del motor de datos (PLAN_RENTA_VARIABLE.md, fase 2), que necesita tu OK.

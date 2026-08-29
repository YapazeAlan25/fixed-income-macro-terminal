# Terminal de Renta Fija y Macroeconomía Argentina

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-Dashboard-FF4B4B?logo=streamlit&logoColor=white)
![DuckDB](https://img.shields.io/badge/DuckDB-Warehouse-FFF000?logo=duckdb&logoColor=black)
![Prefect](https://img.shields.io/badge/Prefect-Orquestación-070E10?logo=prefect&logoColor=white)
![Estado](https://img.shields.io/badge/Estado-en%20producción%20diaria-2ea44f)
![Código](https://img.shields.io/badge/Código-privado%20%7C%20repo%20de%20portfolio-lightgrey)

Escritorio de análisis financiero propio que cubre bonos argentinos (pricing y arbitraje), la economía real de Argentina (actividad, empleo, precios, fiscal, sector externo) y las variables globales que mueve el mercado (tasas americanas, estrés sistémico, curvas de recesión). No es solo un pricer de bonos: es un tablero completo, del tipo que un analista de mesa consulta todos los días para tener la foto completa antes de tomar una posición.

> 📌 **Este repositorio es una vidriera del proyecto.** Documenta su funcionamiento y resultados con capturas reales. El código fuente es privado; este README explica arquitectura, alcance y stack técnico.

**Autor:** Alan Yapaze — [LinkedIn](https://www.linkedin.com/in/alan-yapaze/)
**Vigencia:** 2023 – presente (iniciado como modelado manual en Excel, migrado en 2026 a una arquitectura Python automatizada)

## Índice

- [El proyecto en números](#el-proyecto-en-números)
- [Qué es](#qué-es)
- [Arquitectura](#arquitectura)
- [Cobertura de datos](#cobertura-de-datos)
- [Pipeline de ejecución](#pipeline-de-ejecución)
- [Capturas](#capturas)
- [Qué resuelve](#qué-resuelve)
- [Motores de pricing](#motores-de-pricing)
- [Otros motores destacados](#otros-motores-destacados)
- [Lógica destacada (ejemplo ilustrativo)](#lógica-destacada-ejemplo-ilustrativo)
- [Stack técnico](#stack-técnico)
- [Roadmap](#roadmap)
- [Contacto](#contacto)

## El proyecto en números

| | |
|---|---|
| **13** | fuentes de datos integradas (BCRA, INDEC, FRED, ECB, BIS, Treasury, OECD, datos.gob.ar, UTDT, mercados AR, yfinance, Binance, MAE/IOL) |
| **200+** | instrumentos de renta fija cubiertos (36 soberanos/provinciales + 189 Obligaciones Negociables corporativas + letras y bonos en pesos) |
| **7** | tipos de instrumento con motor de pricing propio |
| **10** | categorías de economía real y mercado cubiertas para Argentina |
| **~330** | funciones de acceso a datos sobre el warehouse (una por serie o grupo de series) |
| **27** | parsers propios para los distintos módulos de Excel oficiales de INDEC |
| **~45** | gráficos interactivos solo en el escritorio de Macro Argentina |

## Qué es

Un sistema de dos capas construido para reemplazar el trabajo manual de armar y actualizar planillas de bonos y macro todos los días:

1. **Capa de datos (ETL):** ingesta automatizada desde BCRA, INDEC, FRED/ECB/BIS/Treasury/OECD, datos.gob.ar y prospectos de bonos del MAE, hacia un data warehouse analítico en DuckDB/Parquet.
2. **Capa de análisis y presentación:** motores de pricing y detección de arbitraje propios para renta fija argentina —orquestados con Prefect—, más un escritorio macro (Argentina y global) interactivo en Streamlit.

La lógica financiera y las decisiones de arquitectura son de autoría propia; la implementación de código fue desarrollada con asistencia de agentes de IA (Claude, Gemini) bajo un enfoque de *AI-augmented development* — dirigido por criterio financiero, no por experiencia previa como programador.

## Arquitectura

<img src="assets/architecture.svg" alt="Arquitectura: fuentes de datos → motor de ingesta → warehouse DuckDB/Parquet → motores de pricing y capa de señales → dashboard Streamlit" width="100%" />

Dos motores privados —uno de ingesta, uno de análisis— que comparten el mismo warehouse. La descarga de cada fuente corre con su propio manejo de rate-limits y caché incremental (no vuelve a traer lo que no cambió); el pricing y las señales se recalculan sobre esa foto ya consolidada, orquestados como un pipeline de 5 fases con Prefect.

## Cobertura de datos

**Economía real y variables de mercado — Argentina** (10 categorías): Cambiario · Monetario · Actividad · Fiscal · Empleo y Sociedad · Precios · Consumo y Retail · Sector Externo · Expectativas · Mercados.

**Variables globales que mira el mercado**: bancos centrales (Fed, ECB, BOJ, PBOC) y su liquidez agregada · curvas del Tesoro americano y TIPS · inflación breakeven · índice de estrés sistémico (VIX, MOVE, VVIX, CISS) · curvas de recesión (T10Y2Y, T10Y3M) · fiscal de EE.UU.

**Universo de renta fija argentina**: Tasa fija, CER, Dollar Linked, Duales, Soberanos/Provinciales USD y 189 Obligaciones Negociables corporativas (ley local y extranjera), con curva Nelson-Siegel ajustada en vivo para cada clase de emisor.

## Pipeline de ejecución

El sistema corre en un orden fijo de 4 pasos, repartidos entre este repo y su capa de datos (ETL):

1. **Universo de bonos** — sincroniza el universo completo de Títulos Públicos, Obligaciones Negociables y Fideicomisos Financieros del MAE (no solo los "vigentes" que expone la API oficial).
2. **Macro global** — descarga masiva desde BCRA, INDEC, FRED/ECB/Treasury, datos.gob.ar y arma el índice unificado de series.
3. **Precios en vivo** — actualiza intradiario los futuros de dólar (scraping de fuentes públicas de mercado) y los precios de bonos/letras/ON vía IOL.
4. **Motor de pricing y dashboard** — corre el ETL de planillas BCRA/INDEC, dispara el pipeline de pricers (Prefect) y levanta el escritorio en Streamlit.

## Capturas

> Todas las capturas son de una corrida real del sistema, sin datos de ejemplo.

### Escritorio Macro Argentina — panel general
Estado de reservas, tasa real, brecha cambiaria, riesgo país, resultado fiscal, actividad y desempleo en un solo panel; reservas del BCRA y riesgo país (EMBI+) con historia completa.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/adab3b11-e626-4e99-9a59-e0429897974b" />

### Cambiario — brecha y evolución de tipos de cambio
"La Trinidad Macro" (inflación mensual, tasa de interés y crawling peg en un mismo eje), brecha CCL/oficial histórica, y evolución completa de los distintos tipos de cambio desde convertibilidad.

<img width="3120" height="910" alt="image" src="https://github.com/user-attachments/assets/4be9210a-88d0-4c1f-a581-7d5912f81282" />

### Inflación (IPC) por rubro
Serie histórica completa del IPC (incluye la hiperinflación de 1989-90), apertura por rubro y comparación núcleo vs. estacional vs. regulados — construido sobre el parser propio de los Excel de INDEC.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/dbac7f9c-6817-4cb8-b141-794d8cfb5197" />

<!--
📌 4 capturas que le faltan a esta sección para mostrar la cobertura completa de Macro Argentina
   (agregalas donde corresponda, con el mismo formato que las de arriba):

### Inicio — brief matutino
El primer panel que ve el usuario al abrir el sistema: banderas de riesgo (macro global y
riesgo Argentina), cuadro de los 6 tipos de cambio con brecha vs. oficial, y contexto
overnight (futuros de EE.UU., mercados asiáticos, commodities agro).

[PEGAR CAPTURA ACÁ]

### Monetario — agregados y tasas de referencia
M1/M2/M3 y base monetaria, BADLAR/TAMAR, balance del BCRA — el tablero de política monetaria
completo, hoy sin ninguna captura en el repo pese a ser una de las secciones más grandes.

[PEGAR CAPTURA ACÁ]

### Fiscal — resultado y dinámica de financiamiento (rollover)
Resultado primario/financiero del Sector Público Nacional y emisión vs. amortización de
deuda en moneda local y extranjera — el termómetro de cuánta deuda nueva coloca el Tesoro
contra cuánta vence.

[PEGAR CAPTURA ACÁ]

### Consumo y Retail — medios de pago
Evolución de la preferencia del consumidor entre efectivo y tarjetas (débito/crédito) en
supermercados — un gráfico simple y muy legible incluso para un lector no técnico.

[PEGAR CAPTURA ACÁ]
-->

### Tasa Fija y CER — curvas con ajuste Nelson-Siegel
Curvas de rendimiento sobre precios spot para renta fija a tasa fija y ajustada por CER, con la tabla de pricing completa (TIR, TEA, TEM, Duration, Convexidad) detrás de cada punto.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/07d48975-d71f-4f66-95c0-38cdb5769081" />
<img width="3061" height="486" alt="image" src="https://github.com/user-attachments/assets/8d23181e-dda8-4682-8589-b73afb7d7573" />

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/ed736640-3de1-401f-a228-f55862fa6f4a" />
<img width="2723" height="424" alt="image" src="https://github.com/user-attachments/assets/bbabf686-78ba-47dc-9ae7-847d3e77aba8" />

### Dollar Linked — bandas cambiarias y proyección por escenario
Evolución del A3500 contra el régimen oficial de bandas cambiarias, con brecha promedio (6M e histórica) contra el límite superior e inferior; dos escenarios de proyección — devaluación implícita en los futuros A3500 y en los futuros ROFEX — para comparar contra la curva de tasa fija.

<img width="3048" height="863" alt="image" src="https://github.com/user-attachments/assets/25858673-3b7c-4007-8a83-d8372e78de8e" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/79186c45-e6b9-47e2-9b0d-6eb7cb3ac6db" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/353fb007-1041-4347-aa56-f4849985a2b0" />

### Soberanos y Provinciales USD — MEP implícito y arbitraje legislativo
Ratios de dólar MEP implícito por activo, spread en bps entre pares de bonos soberanos bajo ley local vs. extranjera (AL29/GD29, AL30/GD30, etc.), y curvas por ley (local, extranjera, deuda provincial y Bopreales).

<img width="3135" height="948" alt="image" src="https://github.com/user-attachments/assets/ab120e75-0331-40e0-8b04-f66d2e2d2764" />
<img width="3082" height="1155" alt="image" src="https://github.com/user-attachments/assets/212a6a9d-4259-46ef-af10-d587e3f10251" />
<img width="3024" height="419" alt="image" src="https://github.com/user-attachments/assets/df62938f-b931-46e8-9083-773833e7facb" />

### Corporativos USD — 189 Obligaciones Negociables por ley y por sector
Universo completo de ONs corporativas, segmentadas por ley de emisión (local/extranjera) y por sector económico (agrupa emisores como YPF/PAE/Vista bajo "Oil & Gas", exige un mínimo de 3 tickers para que un sector tenga curva propia).

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/61cf94a6-2f07-4186-b72c-1c637ea83bc4" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/d69df2f4-1b1b-4602-b258-fea9868014be" />

<!--
📌 Falta la captura de "Ficha de bono" — el detalle de sensibilidad de un instrumento puntual
   (paridad, TIR, current yield, Duration, Convexidad, variación de precio ante ±100bps).
   Ninguna de las capturas actuales corresponde a esta vista; si existe, va acá:

### Ficha de bono: sensibilidad y duration
Métricas spot y de sensibilidad para cualquier bono del universo, drill-down desde cualquiera
de las tablas de pricing de arriba.

[PEGAR CAPTURA ACÁ]
-->

### Tablero de Arbitraje: señales cuantitativas de trading
Dos motores de detección de desfasajes de precio, con acción sugerida (comprar/vender) calculada en vivo:

- **Inflación implícita vs. mercado (BEI vs. REM):** empareja una Lecap y un bono CER que vencen el mismo día para extraer la inflación que el mercado está pricing (BEI) y la compara contra el consenso de analistas (REM). Si el bono CER rinde tan poco que asume una inflación irrealmente alta, señala vender ese CER y pasarse a la Lecap (LONG FIJA).
- **Arbitraje FX sintético (Dollar Linked vs. Tasa Fija):** simula el rendimiento de un bono Dollar Linked asumiendo que el dólar devalúa según lo que pricean los futuros de dólar (obtenidos por scraping), y lo compara contra una Lecap del mismo plazo — si la devaluación implícita en el Dollar Linked es menor a la que rendiría la tasa fija, señala vender el Dollar Linked y pasarse a tasa fija.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/2ef430c2-09e0-4567-8d81-7563a97364a5" />

### Semáforo de estrés sistémico global (GSSI) y tasas americanas
Índice compuesto de estrés de mercado (percentiles históricos de VIX, MOVE, VVIX, CISS), curva del Tesoro americano, TIPS, inflación breakeven, y las dos curvas que la propia Fed de Nueva York usa como modelo oficial de probabilidad de recesión (T10Y2Y y T10Y3M), con historia desde fines de los '70.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/0bad6d37-7111-44b8-b031-99605fc0d881" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/e0ee9e9d-ab98-4d65-bddf-d76dd59bd429" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/6195296e-12a4-4324-ae8f-eb49c403ff0e" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/af55d776-1bbe-445c-862a-ff389ca0de82" />
<img width="2051" height="489" alt="image" src="https://github.com/user-attachments/assets/9ef78718-2894-45e6-af0e-c7a500636725" />

## Qué resuelve

Antes de este sistema, armar la foto diaria del mercado de renta fija argentino significaba actualizar planillas de Excel a mano: bajar precios, recalcular TIR y duration bono por bono, y cruzar manualmente con la macro del día. Este sistema automatiza esa cadena completa y agrega capacidades que no existían en la versión manual: ajuste de curvas por Nelson-Siegel, detección de arbitraje legislativo y de arbitraje FX sintético con acción sugerida, y un panel de riesgo sistémico global actualizado en vivo — todo cruzado contra el contexto macro real de Argentina y del mundo, no solo el precio del bono en el vacío.

## Motores de pricing

Cobertura de 7 tipos de instrumentos de renta fija argentina, sobre un universo de más de 200 activos (36 soberanos/provinciales + 189 Obligaciones Negociables corporativas + Letras y bonos en pesos):

- Tasa fija (soberana, provincial, corporativa)
- CER (ajustados por inflación — lookup a T-10 sobre CER Base y CER de liquidación/vencimiento, más un proyector propio que extiende la curva a futuro con el REM cuando aún no hay CER oficial publicado)
- Dollar-linked
- Duales (con ruteo automático por pata ganadora)
- Hard dollar (soberanos y corporativos en USD, ley local y extranjera)

Cálculos incluidos: NPV, Duration Macaulay/Modificada, Convexidad, Z-spread contra la curva del Tesoro americano interpolada, y solvers de TIR propios — un **solver por método de Brent optimizado con Numba (JIT)** para los instrumentos en pesos (tasa fija, CER, duales), y `scipy.optimize.brentq` para los bonos hard-dollar.

## Otros motores destacados

- **Calculadora de FX implícito (MEP/CCL)**: prioriza fuente de precio (API de bróker → ratio AL30/GD30 → A3500) y descarta automáticamente cotizaciones con más de 30 días de antigüedad.
- **Motor de régimen macro**: construye una matriz diaria de régimen con un *firewall* anti-look-ahead-bias (retrasa 2 días las variables del BCRA para simular el rezago real de publicación), más un "Shadow FX" (tipo de cambio implícito por M2/reservas) y un Z-score móvil (ventana de 90 días) del spread interbancario BAIBAR-TAMAR.
- **Semáforo de Estrés Sistémico Global (GSSI)**: índice compuesto 0-100 por percentil histórico de VIX, MOVE, VVIX y CISS, con su variación mensual como referencia de tendencia.
- **Proyector de CER por REM**: cuando el BCRA todavía no publicó el CER oficial de una fecha futura, bootstrapea la curva de inflación mensual implícita en el REM (Relevamiento de Expectativas de Mercado) y la proyecta día a día, respetando qué mes de IPC corresponde legalmente aplicar según el día del mes.

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
| Cálculo numérico | NumPy, SciPy, Numba (JIT) |
| Fuentes de datos | API BCRA, INDEC (parsers de Excel oficiales), FRED, ECB, BIS, Treasury, OECD, datos.gob.ar, prospectos MAE (PDF), precios IOL, futuros de dólar (scraping) |
| Extracción de fichas de bonos | PyMuPDF (texto) + parsing automático por expresiones regulares del documento legal definitivo; lectura asistida por LLM en el flujo de carga manual de casos no estándar |
| Generación de reportes | fpdf2 (fichas técnicas en PDF) |
| Dashboard | Streamlit, Plotly |
| Validación | pytest |

## Roadmap

- **Portfolio tracking y optimización por duration target**: ya prototipado (carga de posiciones propias + optimizador de cartera con restricciones — TIR mínima, concentración máxima por activo, duration objetivo); en revisión antes de reintegrarlo al dashboard en vivo.
- **Reservas internacionales NETAS** (brutas − encajes en USD − swaps): hoy el panel muestra solo el proxy bruto por falta de una fuente pública confiable de encajes/swaps.
- **Exportación de reportes PDF/Excel** con fichas técnicas por bono: el motor está construido (`fpdf2` + formato Excel) pero la fase queda deshabilitada en el pipeline por ahora, pendiente de reactivar.

## Contacto

¿Preguntas sobre el proyecto o interés en el desarrollo? [Alan Yapaze en LinkedIn](https://www.linkedin.com/in/alan-yapaze/)

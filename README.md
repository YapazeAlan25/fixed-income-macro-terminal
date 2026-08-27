# Terminal de Renta Fija y Macroeconomía Argentina

Sistema propio de análisis cuantitativo para el universo de deuda soberana, provincial y corporativa argentina, con un escritorio macro que cruza variables locales (BCRA, INDEC) y globales (FRED).

> 📌 **Este repositorio es una vidriera del proyecto.** Documenta su funcionamiento y resultados con capturas reales. El código fuente es privado; este README explica arquitectura, alcance y stack técnico.

**Autor:** Alan Yapaze — [LinkedIn](https://www.linkedin.com/in/alan-yapaze/)
**Vigencia:** 2023 – presente (iniciado como modelado manual en Excel, migrado en 2026 a una arquitectura Python automatizada)

---

## Qué es

Un sistema de dos capas construido para reemplazar el trabajo manual de armar y actualizar planillas de bonos y macro todos los días:

1. **Capa de datos (ETL):** ingesta automatizada desde BCRA, INDEC, FRED, datos.gob.ar y prospectos de bonos del MAE, orquestada con Prefect, hacia un data warehouse analítico en DuckDB/Parquet.
2. **Capa de análisis y presentación:** motores de pricing propios para renta fija argentina y un dashboard interactivo en Streamlit para explorar el resultado.

La lógica financiera y las decisiones de arquitectura son de autoría propia; la implementación de código fue desarrollada con asistencia de agentes de IA (Claude, Gemini) bajo un enfoque de *AI-augmented development* — dirigido por criterio financiero, no por experiencia previa como programador.

## Capturas

### Escritorio Macro Argentina
Estado de reservas, tasa real, brecha cambiaria, riesgo país, resultado fiscal, actividad y desempleo en un solo panel, con series históricas del BCRA.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/adab3b11-e626-4e99-9a59-e0429897974b" />
<img width="3120" height="910" alt="image" src="https://github.com/user-attachments/assets/4be9210a-88d0-4c1f-a581-7d5912f81282" />

### Inflación (IPC) por rubro
Serie histórica completa del IPC, apertura por rubro y comparación núcleo vs. estacional vs. regulados — construido sobre el parser propio de los Excel de INDEC.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/dbac7f9c-6817-4cb8-b141-794d8cfb5197" />

### Curvas de Renta Fija Argentina
Curvas de rendimiento precios spot.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/07d48975-d71f-4f66-95c0-38cdb5769081" />
<img width="3061" height="486" alt="image" src="https://github.com/user-attachments/assets/8d23181e-dda8-4682-8589-b73afb7d7573" />

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/ed736640-3de1-401f-a228-f55862fa6f4a" />
<img width="2723" height="424" alt="image" src="https://github.com/user-attachments/assets/bbabf686-78ba-47dc-9ae7-847d3e77aba8" />

<img width="3048" height="863" alt="image" src="https://github.com/user-attachments/assets/25858673-3b7c-4007-8a83-d8372e78de8e" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/79186c45-e6b9-47e2-9b0d-6eb7cb3ac6db" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/353fb007-1041-4347-aa56-f4849985a2b0" />

<img width="3135" height="948" alt="image" src="https://github.com/user-attachments/assets/ab120e75-0331-40e0-8b04-f66d2e2d2764" />
<img width="3082" height="1155" alt="image" src="https://github.com/user-attachments/assets/212a6a9d-4259-46ef-af10-d587e3f10251" />
<img width="3024" height="419" alt="image" src="https://github.com/user-attachments/assets/df62938f-b931-46e8-9083-773833e7facb" />

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/61cf94a6-2f07-4186-b72c-1c637ea83bc4" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/d69df2f4-1b1b-4602-b258-fea9868014be" />


### Breakeven Inflation
El objetivo es encontrar desfasajes groseros entre la inflación que pricean los bonos (BEI) y la inflación real esperada (REM). El sistema empareja una Lecap y un Boncer que vencen el mismo día. Si el Boncer rinde tan poco que asume una inflación irrealmente alta, te avisa para que lo vendas y compres la Lecap (LONG FIJA).

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/2ef430c2-09e0-4567-8d81-7563a97364a5" />

### Semáforo de estrés sistémico global (GSSI) y tasas americanas
Índice compuesto de estrés de mercado (percentiles históricos de VIX, MOVE, VVIX, CISS) junto a la curva del Tesoro americano, TIPS e inflación breakeven.

<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/0bad6d37-7111-44b8-b031-99605fc0d881" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/e0ee9e9d-ab98-4d65-bddf-d76dd59bd429" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/6195296e-12a4-4324-ae8f-eb49c403ff0e" />
<img width="3440" height="1440" alt="image" src="https://github.com/user-attachments/assets/af55d776-1bbe-445c-862a-ff389ca0de82" />
<img width="2051" height="489" alt="image" src="https://github.com/user-attachments/assets/9ef78718-2894-45e6-af0e-c7a500636725" />

## Qué resuelve

Antes de este sistema, armar la foto diaria del mercado de renta fija argentino significaba actualizar planillas de Excel a mano: bajar precios, recalcular TIR y duration bono por bono, y cruzar manualmente con la macro del día. Este sistema automatiza esa cadena completa y agrega capacidades que no existían en la versión manual: ajuste de curvas por Nelson-Siegel, detección de arbitraje legislativo, y un panel de riesgo sistémico global actualizado en vivo.

## Motores de pricing

Cobertura de 7 tipos de instrumentos de renta fija argentina:

- Tasa fija (soberana, provincial, corporativa)
- CER (ajustados por inflación)
- Dollar-linked
- Duales (con ruteo automático por pata ganadora)
- Hard dollar (soberanos y corporativos en USD, ley local y extranjera)

Cálculos incluidos: NPV, Duration Macaulay/Modificada, Convexidad, y un **solver de TIR propio por método de Brent** (híbrido bisección / interpolación cuadrática inversa), optimizado con Numba (JIT) para performance.

## Stack técnico

| Capa | Tecnologías |
|---|---|
| Orquestación ETL | Prefect |
| Almacenamiento analítico | DuckDB, Parquet |
| Cálculo numérico | NumPy, SciPy, Numba (JIT) |
| Fuentes de datos | API BCRA, INDEC (parsers de Excel oficiales), FRED, datos.gob.ar, prospectos MAE (PDF) |
| Extracción de fichas de bonos | PyMuPDF + LLM para estructurar parámetros de prospectos |
| Generación de reportes | fpdf2 (fichas técnicas en PDF) |
| Dashboard | Streamlit, Plotly |
| Validación | pytest |

## Contacto

¿Preguntas sobre el proyecto o interés en el desarrollo? [Alan Yapaze en LinkedIn](https://www.linkedin.com/in/alan-yapaze/)

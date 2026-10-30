# Roadmap

- **Informes y sitio de análisis.** Un generador de informes (HTML y PDF) que arma, desde el propio sistema, informes sobre postura monetaria, inflación, actividad y deuda, con sus límites y fuentes a la vista, y un sitio estático que los publique. Sin recomendaciones de inversión.
- **Registro de pronósticos.** Cada lectura del sistema queda registrada y se contrasta con lo que pasa después; con las ventanas actuales las primeras conclusiones llegan en 2027-2028 y para valuación y burbujas tardará años (se dice en pantalla).
- **Calendario oficial del BCRA.** Hoy las fechas de publicación del BCRA se estiman con la última publicación observada; el calendario oficial las reemplazará cuando se cargue.
- **Control de frescura en todas las fuentes**: hoy cubre los 23 módulos del INDEC; llevarlo a BCRA, ARCA y Secretaría de Finanzas, que tienen su propio calendario.
- **Armador con cardinalidad exacta en Markowitz**: hoy elige los bonos por Sharpe individual y optimiza pesos continuos sobre ese conjunto; una formulación entera con riesgo sobre todo el universo evitaría la preselección. Y ampliar la historia de precios para que los bonos recién emitidos o poco operados entren en las métricas de riesgo.
- **Historia de precios para Dollar Linked y duales**: hoy no se conserva el dólar futuro vigente de cada día, por lo que su TIR histórica no se reconstruye.
- **Indicadores prudenciales que solo el BCRA da en prosa** (por ejemplo PDE y LCR) y **más explotación de datos oficiales ya descargados** (seguridad social por tamaño de empleador y obra social, colocaciones en dólares y canjes del Tesoro).
- **Cronogramas de pago desde un endpoint estructurado**: hoy la fuente de los flujos es el prospecto en PDF verificado en capas; se evalúa si MAE o BYMA exponen los cronogramas por API.

---
[← Volver al inicio del repositorio](../README.md)

# Workflow n8n: Cualificación y Enriquecimiento de Leads con IA

Este workflow automatiza el proceso de cualificar y enriquecer una lista de leads extraída de Google Sheets, utilizando IA para completar la información faltante.

## Flujo del Proceso

1.  **Lectura y Limpieza:**
    - Se activa manualmente y lee una lista de leads en bruto desde un Google Sheet.
    - Realiza una limpieza de datos: elimina duplicados y selecciona solo un contacto "decisor" (ej. C-Level, Director) por empresa, descartando el resto.

2.  **Enriquecimiento con IA:**
    - Para los leads que no tienen email o descripción, el workflow realiza *scraping* de su página web.
    - Utiliza un modelo de **Google Gemini** para analizar el contenido web, extraer un email de contacto y generar una descripción concisa de la empresa.

3.  **Clasificación y Escritura:**
    - Une los datos originales con los recién enriquecidos.
    - Guarda los resultados en dos hojas de Google Sheets separadas (que se limpian en cada ejecución):
        - Una para los leads cualificados y enriquecidos.
        - Otra para aquellos a los que no se les pudo encontrar email.

## Componentes Clave

- **n8n**: Orquestador del proceso.
- **Google Sheets**: Fuente de datos de entrada y destino de salida.
- **Google Gemini**: Modelo de IA para el enriquecimiento de datos.

## Configuración Requerida

- `Google Sheets OAuth2 API`
- `Google Gemini (PaLM) Api`

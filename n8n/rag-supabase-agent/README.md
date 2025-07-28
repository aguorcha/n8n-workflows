# Workflow n8n: Agente RAG con Base de Conocimiento en Google Drive y Supabase

Este workflow de n8n implementa un sistema completo de **Retrieval-Augmented Generation (RAG)**. Se encarga de construir y mantener automáticamente una base de conocimiento a partir de archivos en Google Drive y, al mismo tiempo, ofrece un agente conversacional para que los usuarios puedan consultar esa información.

## Funcionalidades Principales

El workflow se divide en dos lógicas principales que funcionan en paralelo:

### 1. Creación y Mantenimiento de la Base de Conocimiento
Esta parte del flujo se activa automáticamente para mantener la base de datos vectorial siempre actualizada.

- **Trigger**: Se dispara cuando un archivo es **creado o actualizado** en una carpeta específica de Google Drive.
- **Procesamiento**:
    - Si un archivo se actualiza, primero elimina la versión anterior de la base de datos de Supabase para evitar duplicados.
    - Descarga el contenido del archivo (soporta PDF, Google Docs, CSV, XLSX).
    - Extrae el texto plano del documento.
    - Genera *embeddings* (vectores numéricos) a partir del texto usando el modelo de OpenAI.
    - Almacena estos vectores en una tabla de **Supabase Vector Store**, creando así la base de conocimiento.

### 2. Agente Conversacional para Consultas
Esta parte expone un endpoint de chat para que un usuario pueda interactuar con la información.

- **Trigger**: Se activa cuando un usuario envía un mensaje a través del **Chat Trigger** de n8n.
- **Agente Inteligente**:
    - Un agente de LangChain procesa la pregunta del usuario.
    - Utiliza una herramienta (`base_de_datos`) que busca la información más relevante dentro de Supabase Vector Store.
    - Envía el contexto recuperado y la pregunta original a un modelo de lenguaje de **OpenAI (GPT-4o mini)** para generar una respuesta coherente y precisa.
    - Utiliza una base de datos **PostgreSQL** para mantener la memoria de la conversación, permitiendo un diálogo con contexto.

## Componentes y Tecnologías Utilizadas

- **n8n**: Orquestador principal del flujo.
- **Google Drive**: Almacén de los documentos fuente.
- **Supabase**: Utilizado como base de datos vectorial (Vector Store) para el RAG.
- **OpenAI**: Para la generación de embeddings y como modelo de lenguaje del agente.
- **PostgreSQL**: Para gestionar la memoria y el historial del chat.

## Configuración Requerida

Para que este workflow funcione, es necesario configurar las siguientes credenciales en n8n:

- `Google Drive OAuth2 API`
- `Supabase API`
- `OpenAI API`
- `Postgres`

## Uso

1.  **Poblar la base de conocimiento**: Sube o actualiza archivos en la carpeta de Google Drive configurada. El workflow los procesará automáticamente.
2.  **Consultar al agente**: Envía peticiones POST al webhook del nodo `When chat message received` con el input del usuario en el cuerpo de la solicitud.

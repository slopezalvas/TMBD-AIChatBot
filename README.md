# 🎬 Cine Assistant - AI Chatbot

Este proyecto es un asistente de cine inteligente capaz de razonar, recordar los gustos del usuario y buscar películas en tiempo real. No es un simple "wrapper" de ChatGPT; es un sistema integrado que utiliza herramientas nativas de IA, persistencia avanzada en base de datos y consumo de APIs externas con lógica de filtrado profesional.

**Link del Deploy:** [https://my-app-mu-eight-98.vercel.app](https://my-app-mu-eight-98.vercel.app)

<img width="1857" height="940" alt="image" src="https://github.com/user-attachments/assets/2f6def79-2960-441e-96f4-66e67991275a" />



## 🚀 Propuesta de Valor

### El Problema que resuelve

La búsqueda de películas suele ser fragmentada y carece de personalización real. Los usuarios deben navegar por menús complejos o repetir sus preferencias en cada sesión.

### Nuestra Solución

**Cine Assistant** centraliza la experiencia mediante una IA con "memoria" a largo plazo. El bot no solo busca títulos, sino que **descubre** contenido basándose en un perfil de usuario que se construye dinámicamente en una base de datos PostgreSQL, ofreciendo una experiencia conversacional, multilingüe y visualmente rica.

### Público Objetivo

Entusiastas del cine que buscan recomendaciones precisas y una interfaz minimalista pero potente.


## 🛠️ Stack Tecnológico

- **IA Engine:** OpenAI GPT-4o mediante **Vercel AI SDK** (Modelos de lenguaje y streaming).
- **Frontend:** **Next.js 15** (App Router) + Tailwind CSS.
- **Base de Datos:** PostgreSQL (**Neon.tech**).
- **ORM:** **Drizzle ORM** (Manejo de esquemas relacionales y tipos JSONB).
- **API Externa:** TMDB (The Movie Database).
- **Validación:** **Zod** (Validación estricta de contratos de API y esquemas de Tools).
- **Estado y Persistencia:** `useSyncExternalStore` de React para manejo puro de estados externos.


## 📂 Estructura del Proyecto

``` text
my-app/
├── app/
│   ├── api/
│   │   ├── chat/route.ts      # Orquestador principal del LLM y Tools
│   │   ├── history/route.ts   # Recuperación de historial persistente
│   │   └── feedback/route.ts  # Procesamiento de ratings (👍/👎)
│   ├── layout.tsx             # Configuración de fuentes y Root Provider
│   └── page.tsx               # Contenedor principal de la aplicación
├── components/
│   ├── chat-box.tsx           # UI principal del Chat (Estado y lógica de UI)
│   ├── chat-message.tsx       # Renderizado de mensajes (Markdown + Posters)
│   ├── debug-panel.tsx        # Visualización de logs y razonamiento del LLM
│   └── client-only.tsx        # Wrapper para evitar errores de hidratación
├── lib/
│   ├── ai/
│   │   ├── prompts.ts         # Definición de System Prompts dinámicos
│   │   └── tools.ts           # Definición y ejecución de Tool Calling
│   ├── db/
│   │   ├── index.ts           # Conexión con Neon (Postgres) via Drizzle
│   │   └── schema.ts          # Definición de tablas y esquemas relacionales
│   └── services/
│       ├── movies.ts          # Integración con TMDB (Búsqueda y Discovery)
│       ├── genres.ts          # Sincronización y manejo de géneros
│       ├── preferences.ts     # CRUD de preferencias de usuario
│       └── chat.ts            # Lógica de guardado de historial
├── drizzle.config.ts          # Configuración de migraciones de la DB
└── next.config.ts             # Configuración de Next.js (Remote Patterns de imágenes)
```


## 📋 User Stories

1. **Búsqueda e Intención Inteligente:**
   - **Como** usuario cinéfilo,
   - **Quiero** pedir recomendaciones complejas (ej. "dame un thriller de los 90"),
   - **Para** obtener resultados ordenados por popularidad con posters y sinopsis reales.

2. **Memoria Persistente de Gustos:**
   - **Como** usuario recurrente,
   - **Quiero** que el bot aprenda mi nombre e intereses,
   - **Para** recibir un trato personalizado sin tener que reintroducir mis datos.

3. **Feedback y Mejora Continua:**
   - **Como** usuario,
   - **Quiero** calificar las respuestas del bot (👍/👎),
   - **Para** que el sistema registre la calidad de las recomendaciones.


## 🧠 Decisiones Técnicas y Criterio de Ingeniería

### 1. Lógica de "Discovery" sobre "Search"

A diferencia de implementaciones básicas que solo usan búsqueda por texto, este bot utiliza el endpoint `discover/movie` de TMDB. Esto permite al LLM filtrar por **IDs de género dinámicos**, año de estreno y popularidad, garantizando resultados de alta calidad frente a coincidencias textuales irrelevantes.

### 2. Programación Defensiva con Zod

Se implementaron esquemas de validación con **Zod** para todas las respuestas de la API externa. Esto asegura que, ante cualquier cambio inesperado en el contrato de TMDB, la aplicación "falle con gracia" (Graceful Failure) en lugar de romper la interfaz del usuario o el contexto del LLM.

### 3. Pureza en React y Manejo de Hidratación

Se utilizó el API moderno `useSyncExternalStore` para gestionar la persistencia del `userId` en `localStorage`. Esto resuelve errores críticos de hidratación en Next.js y garantiza que el componente sea puro e idempotente, cumpliendo con las reglas de renderizado de React 18+.

### 4. Sincronización Automática (Auto-Seeding)

La aplicación cuenta con una lógica de auto-semillado. Si la tabla de géneros en la DB está vacía, el sistema sincroniza automáticamente los datos desde TMDB en la primera consulta, optimizando la latencia y la experiencia del desarrollador (DX).

## ⚙️ Instalación Local

1. Clonar el repositorio:

   ```bash
   git clone https://github.com/slopezalvas/my-app
   cd my-app
2. Instalar dependencias:

    ```bash
    pnpm install
3. Configurar variables de entorno (.env.local):

    ```env
    AI_GATEWAY_API_KEY=tu_api_gatewat_key_aqui
    OPENAI_API_KEY=tu_key_aqui
    DATABASE_URL=tu_url_de_neon_aqui
    TMDB_TOKEN=tu_bearer_token_de_tmdb_aqui
4. Sincronizar la base de datos:

    ```bash
    pnpm drizzle-kit push
5. Iniciar el servidor:

    ```bash
    pnpm dev

## ✨ Features Bonus Implementadas

- **Streaming:** Respuestas en tiempo real para una mejor UX.
- **Tool Calling:** Capacidad de razonamiento para usar APIs externas.
- **Panel de Debug:** Consola lateral para visualizar el proceso interno del LLM.
- **Persistencia Avanzada:** Guardado de historial completo y preferencias específicas.
- **Multilingüe:** Detección y respuesta automática en el idioma del usuario.
- **Optimización de Imágenes:** Uso de next/image para el renderizado de posters.

---
Developed with ❤️ by [![LinkedIn](https://img.shields.io/badge/Sofia_Lopez_Alvas-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/sofia-lopez-alvas/)

# GGML Automation Full Solution

Plataforma full stack que automatiza el procesamiento de documentos (carta porte, pedimentos y adjuntos de correo electrónico), con clasificación de información mediante IA y generación de reportes en Excel.

El proyecto tiene una arquitectura desacoplada: una API central en .NET expone la lógica de automatización, un backend en NestJS gestiona autenticación y datos para el dashboard, y dos clientes (web y móvil) consumen ambos servicios.

## Arquitectura

```
┌─────────────────┐      ┌──────────────────────┐
│   Dashboard      │      │   Mobile App         │
│   (Next.js)      │      │   (React Native)     │
└────────┬─────────┘      └──────────┬───────────┘
         │                           │
         └─────────────┬─────────────┘
                        │
              ┌─────────▼──────────┐
              │  Backend (NestJS)  │
              │  Auth (JWT) + Data │
              └─────────┬──────────┘
                        │
              ┌─────────▼──────────┐
              │  API GGML (.NET)   │
              │  Automatización    │
              └─────────┬──────────┘
                        │
              ┌─────────▼──────────┐
              │     Supabase       │
              └────────────────────┘
```

## Repositorios

| Componente | Descripción | Stack |
|---|---|---|
| [GGML_Automation](https://github.com/OmarZamoranoGarcia/GGML_Automation) | API principal: automatización para el sorteo de tabla de carta porte, lectura de códigos QR de pedimentos, exportación de tablas Access a CSV y clasificación de información mediante IA. | C#, .NET |
| [ggml-automation-nest-backend](https://github.com/OmarZamoranoGarcia/ggml-automation-nest-backend) | Backend del dashboard: autenticación con JWT y gestión de datos, sirve de puente entre los clientes y la API principal. | NestJS, TypeScript |
| [GGML_Automation_Dashboard](https://github.com/OmarZamoranoGarcia/GGML_Automation_Dashboard) | Dashboard web para visualizar y administrar el procesamiento de documentos. | Next.js, React, JavaScript |
| [ggml-automation-mobile-app](https://github.com/OmarZamoranoGarcia/ggml-automation-mobile-app) | Cliente móvil con las mismas funcionalidades del dashboard, para consulta y gestión desde dispositivos móviles. | React Native |

## Flujo de procesamiento (API GGML_Automation)
 
La API en .NET automatiza de punta a punta el procesamiento de los archivos Excel que llegan por correo:
 
1. **Visualización del correo** — se conecta a la bandeja de entrada y spam para tomar los adjuntos de cada correo recibido.
2. **Carga inicial a Supabase** — sube los adjuntos originales para respaldo.
3. **Conversión a CSV** — descarga los archivos Excel y los convierte a formato CSV.
4. **Limpieza con IA** — envía el CSV a la API de Gemini para extraer únicamente los datos de la tabla, descartando ruido y contenido no tabular.
5. **Generación de Excel limpio** — con el CSV ya depurado, genera una tabla de Excel estructurada.
6. **Sorteo y sumas dinámicas** — reordena las columnas del Excel y suma columnas específicas; tanto el criterio de sorteo como las columnas a sumar dependen del remitente del correo (cada cliente/correo tiene su propia configuración de procesamiento).
7. **Carga final a Supabase** — sube el Excel ya sorteado y calculado, listo para su consulta desde el dashboard o la app móvil.

## Autor
 
**Omar Zamorano García**
[GitHub](https://github.com/OmarZamoranoGarcia)

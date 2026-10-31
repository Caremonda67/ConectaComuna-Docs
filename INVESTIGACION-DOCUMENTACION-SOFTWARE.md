# Investigación: Cómo Documentar Proyectos de Software Profesionales

Fecha de elaboración: Octubre 2026.
Objetivo: Definir la estructura, marcos metodológicos y contenidos necesarios para documentar el proyecto Conecta Comuna en tres entregables (Backend técnico, Frontend técnico y Resumen unificado del sistema).

---

## 1. Marcos metodológicos de documentación en la industria

En proyectos de software modernos y de evaluación técnica, la documentación deja de ser un texto plano desordenado y se organiza bajo marcos probados:

### A. Marco Diátaxis (Daniele Procida)
Es el estándar adoptado por organizaciones como Canonical (Ubuntu), NumPy y Gatsby. Clasifica la documentación en cuatro cuadrantes según el objetivo del lector:
1. **Tutoriales (Orientados al aprendizaje):** Guían paso a paso al recién llegado para completar un flujo inicial sin rodeos teóricos.
2. **Guías prácticas / How-To (Orientadas a tareas):** Recetas concretas para resolver problemas específicos (ej. "Cómo agregar una nueva categoría de negocio", "Cómo probar el asistente comunal en local").
3. **Referencia (Orientada a la información técnica):** Descripción exacta y fría de la API, endpoints, esquemas de tablas, variables de entorno y tipos de datos.
4. **Explicación / Arquitectura (Orientada al entendimiento):** Análisis de contexto, decisiones de diseño, razones de elección de librerías y alternativas descartadas.

### B. Architecture Decision Records (ADRs - Estándar Michael Nygard)
Formato para registrar cada decisión de peso técnico tomada durante el desarrollo. Cada registro contiene:
- **Título:** Numerado y descriptivo (ej. "ADR-04: Verificación territorial con el facilitador del barrio").
- **Estado:** Propuesto, Aceptado o Superado.
- **Contexto:** El problema real, las restricciones técnicas y los requerimientos del usuario.
- **Decisión:** La solución exacta acordada.
- **Consecuencias:** Lo que ganamos, lo que asumimos y la deuda técnica controlada.
- **Alternativas consideradas:** Qué otras opciones se evaluaron y por qué se descartaron.

### C. Modelo C4 (Simon Brown)
Estructura en capas para explicar la arquitectura de software a diferentes niveles de zoom:
- **Nivel 1 (Contexto del sistema):** Quiénes usan la plataforma (vecinos, emprendedores, facilitadores) y con qué servicios externos interactúa (Supabase, OpenStreetMap, WhatsApp).
- **Nivel 2 (Contenedores):** La SPA de React, la API REST de Express y la base de datos PostgreSQL en Supabase.
- **Nivel 3 (Componentes):** Cómo se organizan internamente el frontend (páginas, hooks, servicios) y el backend (rutas, middlewares, configuración).
- **Nivel 4 (Código):** Modelos de datos, tipos de dominio y esquemas SQL.

---

## 2. Estructura requerida para el documento de Backend

El documento del backend debe ser técnico, riguroso y detallado. Debe incluir:

1. **Visión del servicio:**
   - Rol del backend en la arquitectura: API REST construida sobre Express y Node.js (módulos ESM), actuando en conjunto con Supabase.
   - Enlace y credenciales de entorno (`.env.example`).
2. **Decisiones técnicas del Backend (ADRs):**
   - Uso de Supabase Auth validado mediante `getUser(token)` en lugar de firmar tokens propios.
   - Manejo de roles (`client`, `business`, `facilitador`) mediante la columna `account_type` sincronizada con triggers de Postgres.
   - Políticas de seguridad Row Level Security (RLS) en la base de datos versus validaciones en código Express.
   - Consultas con ordenamiento espacial y filtros de búsqueda por nombre y barrio.
3. **Catálogo completo de Endpoints (API Reference):**
   - Para cada ruta (`/api/salud`, `/api/auth/*`, `/api/businesses/*`, `/api/orders/*`, `/api/reviews/*`):
     - Método HTTP y ruta exacta.
     - Nivel de acceso (Público, Autenticado, Rol específico).
     - Parámetros de URL / Query params.
     - Estructura del cuerpo de petición (JSON con tipos).
     - Códigos de respuesta HTTP (200, 201, 400, 401, 403, 404, 500) con ejemplos reales de JSON de respuesta y error.
4. **Modelo de datos y Esquema de Base de Datos:**
   - Tabla por tabla (`profiles`, `businesses`, `products`, `orders`, `reviews`, `direcciones_usuario`, `facilitadores_negocio`, etc.).
   - Tipos de columna, restricciones (`PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `DEFAULT`).
   - Tipos ENUM personalizados (`account_type`, `order_status`).
   - Triggers automáticos (`handle_new_user`, cálculo de promedio de reseñas).
5. **Seguridad y buenas prácticas:**
   - CORS configurado para el dominio del frontend.
   - Validación de entradas y sanitización básica.
   - Manejo centralizado de fallos de red hacia Supabase.
6. **Guía de despliegue y pruebas:**
   - Comandos para ejecutar en desarrollo (`node --watch src/server.js`).
   - Variables requeridas en Render (`SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `PORT`).

---

## 3. Estructura requerida para el documento de Frontend

El documento del frontend debe cubrir tanto la experiencia de usuario como la ingeniería de componentes y el rendimiento:

1. **Visión general de la aplicación cliente:**
   - SPA construida con React 19, TypeScript y Vite.
   - Soporte dual: consumo directo de Supabase con sesión activa o modo demo local (`demoBackend`) con datos simulados si la conexión falla o el usuario prueba sin registrarse.
2. **Decisiones técnicas del Frontend (ADRs):**
   - React 19 y compatibilidad estricta con React Compiler (cero cierres obsoletos, estado inmutable, división de formularios con `key` para reiniciar estado sin efectos secundarios).
   - Tailwind CSS con paleta cálida comunitaria, sin sobrecargar la pantalla ni recurrir a degradados genéricos de IA.
   - Leaflet con OpenStreetMap usando componentes con carga diferida (`LazyMap`) para no congelar el navegador móvil.
   - Reportes comunitarios con bandeja de moderación dentro del panel del facilitador, sin sumar un cuarto rol al sistema.
   - Diseño mobile-first: botones y áreas de toque con mínimo 44px de alto para personas con dificultades táctiles.
3. **Mapa de Rutas y Árbol de Vistas:**
   - Rutas públicas (`/`, `/explorar`, `/negocio/:id`, `/mapa`, `/como-funciona`, `/tecnologia`, `/trato-seguro`, `/entrar`, `/registro`).
   - Rutas privadas y protegidas por rol (`/panel`, `/onboarding`, `/panel/negocio`, `/panel/facilitador`).
   - Flujo de redirección inteligente según `account_type` y estado de `onboarding_completado`.
4. **Arquitectura interna:**
   - Capa de Contexto: `AuthContext` (gestión de sesión, usuario activo, negocio del usuario y rol dual).
   - Capa de Servicios: `businessService`, `authService`, `facilitadorService`, `direccionesService`, `demoDataService`.
   - Capa de Hooks personalizados: `useAuth`, `useGeolocation`, `useNeighborhoodLocator`, `useAsync`.
5. **Sistema de Diseño y Accesibilidad:**
   - Tipografía legible con contraste superior al estándar WCAG AA.
   - Manejo de estados de carga (skeletons y textos descriptivos en español cotidiano).
   - Notificaciones y diálogos accesibles mediante atributos ARIA.
6. **Optimización y control de calidad:**
   - Análisis de linter (`oxlint`) con cero advertencias y cero errores.
   - Verificación de tipos con `tsc -b`.
   - Empaquetado de producción con Vite y división en fragmentos (`vendor chunks`).

---

## 4. Estructura requerida para el documento Unificado (General / Resumen)

Este documento no debe ser técnico en exceso, sino claro, estructurado y orientado a que jurados, evaluadores o nuevos desarrolladores entiendan el proyecto en 5 minutos:

1. **Identidad del proyecto:**
   - Nombre: Conecta Comuna.
   - Propósito social: Directorio digital inclusivo para micro-emprendedores barriales con baja alfabetización digital.
   - Categoría Fedesoft: Inclusión y Bienestar.
2. **El problema y la solución:**
   - Barreras del comercio informal y dificultades con plataformas de delivery tradicionales (comisiones altas, interfaces complejas).
   - Propuesta de valor: Directorio directo, pedidos sencillos, contacto por WhatsApp, figura del facilitador comunitario y verificación física en territorio.
3. **Arquitectura general a vuelo de pájaro:**
   - Diagrama de bloques: Frontend (Vercel) <-> Backend API (Render) / Supabase (Postgres + Auth + Storage).
   - Flujo de interacción de punta a punta.
4. **Matriz de Roles del Sistema:**
   - Cliente / Vecino: Busca, filtra por barrio o cercanía, consulta precios, pide por WhatsApp o crea pedidos internos.
   - Emprendedor: Crea perfil de su negocio, fotos, horarios, productos, precios al por mayor y administra pedidos.
   - Facilitador: Apoya a adultos mayores o emprendedores sin teléfono inteligente mediante apadrinamiento con código presencial.
5. **Guía de instalación y puesta en marcha rápida:**
   - Requisitos previos (Node.js 20+).
   - Pasos para arrancar el backend en 3 líneas.
   - Pasos para arrancar el frontend en 3 líneas.
   - Credenciales de prueba y activación del modo demo.
6. **Estado de cumplimiento de la rúbrica Fedesoft:**
   - Puntos fuertes demostrables en código y funcionalidad real.

---

## 5. Criterios de calidad y redacción técnica humana

Para cumplir con las normas del proyecto (`NO-IA-GUIA.md` y `REGLA-BUENAS-PRACTICAS.md`):
- **Cero palabras de relleno:** Prohibido usar "crucial", "fundamental", "robusto", "optimizar", "potenciar", "ecosistema", "apalancar".
- **Datos comprobables:** Citar nombres exactos de funciones, componentes, tablas y comandos.
- **Tono directo:** Estilo de equipo de ingeniería que describe lo que construyó, cómo funciona y qué decisiones tomó.
- **Estructura limpia:** Tablas de referencia, bloques de código con sintaxis resaltada y diagramas de texto legibles.

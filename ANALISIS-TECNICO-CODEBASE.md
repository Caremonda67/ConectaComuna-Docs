# Análisis Técnico Exhaustivo del Codebase

> **Aviso, 29 de octubre.** Este documento es una foto del 15 de octubre y varias partes ya no coinciden con el código: los routers de auth, businesses, reviews y reports se retiraron (ver `DOCUMENTACION-BACKEND.md`), varias rutas que lista el catálogo nunca existieron, y el frontend cambió de estructura. Se conserva como registro del trabajo, no como referencia. El estado actual está en `DOCUMENTACION-BACKEND.md` y `ARQUITECTURA-BACKEND.md`.

Fecha de análisis: Octubre 2026.
Alcance: Inspección completa de los repositorios `backend/` y `frontend/`, verificación de tipos, análisis estático de linter y rectificación de fallos.

---

## 1. Inventario del Backend (`backend/`)

### A. Estructura de archivos
```text
backend/
├── .env.example
├── package.json
├── README.md
├── schema.sql
└── src/
    ├── server.js
    ├── config/
    │   ├── auth.js
    │   └── supabase.js
    └── routes/
        ├── auth.js
        ├── businesses.js
        ├── orders.js
        ├── reviews.js
        └── salud.js
```

### B. Tecnologías y Dependencias
- **Entorno de ejecución:** Node.js v20+ con módulos ESM nativos (`"type": "module"` en `package.json`).
- **Framework web:** Express v4.19.2.
- **Cliente de base de datos:** `@supabase/supabase-js` v2.45.0.
- **Utilidades:** `cors` v2.8.5, `dotenv` v16.4.5.
- **Scripts:** `npm run dev` (`node --watch src/server.js`), `npm start` (`node src/server.js`).

### C. Catálogo de Rutas Backend
| Módulo | Método | Ruta | Acceso | Descripción |
|---|---|---|---|---|
| Salud | `GET` | `/api/salud` | Público | Comprueba el estado del servicio (`{ estado: "ok" }`). |
| Auth | `POST` | `/api/auth/registro` | Público | Registra usuario en Supabase Auth y asigna metadatos (`account_type`, `full_name`, `phone`, `neighborhood`). |
| Auth | `GET` | `/api/auth/mi-cuenta` | Autenticado | Devuelve perfil del usuario según token Bearer JWT. |
| Negocios | `GET` | `/api/businesses` | Público | Lista negocios activos con filtros por `category` y búsqueda de texto `q`. |
| Negocios | `GET` | `/api/businesses/:id` | Público | Devuelve detalle de negocio junto a sus reseñas y datos de clientes. |
| Negocios | `POST` | `/api/businesses` | Rol business | Crea un negocio asignado al `owner_id` autenticado. |
| Negocios | `PATCH` | `/api/businesses/:id` | Dueño | Actualiza campos permitidos del negocio propio. |
| Pedidos | `POST` | `/api/orders` | Autenticado | Crea un pedido para un negocio (valida que el cliente no sea el dueño). |
| Pedidos | `GET` | `/api/orders` | Autenticado | Lista pedidos según rol (`business_id` para negocios, `client_id` para clientes). |
| Pedidos | `PATCH` | `/api/orders/:id/status` | Involucrado | Cambia el estado del pedido (`pending`, `accepted`, `in_progress`, `completed`, `cancelled`). |
| Reseñas | `POST` | `/api/reviews` | Cliente | Crea reseña (1 a 5 estrellas) exigiendo que el pedido esté completado y pertenezca al cliente. |

---

## 2. Inventario del Frontend (`frontend/`)

### A. Estructura de archivos
```text
frontend/
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
├── (sin tailwind.config: con Tailwind v4 los tokens van en src/index.css)
└── src/
    ├── main.tsx
    ├── router.tsx
    ├── index.css
    ├── types/
    │   └── index.ts
    ├── lib/
    │   ├── env.ts
    │   ├── supabase.ts
    │   ├── utils.ts
    │   └── ubicacion.ts
    ├── context/
    │   └── AuthContext.tsx
    ├── hooks/
    │   ├── useAuth.ts
    │   ├── useGeolocation.ts
    │   └── useNeighborhoodLocator.ts
    ├── services/
    │   ├── authService.ts
    │   ├── businessService.ts
    │   ├── demoBackend.ts
    │   ├── demoDataService.ts
    │   ├── direccionesService.ts
    │   ├── facilitadorService.ts
    │   └── ordersService.ts
    ├── components/
    │   ├── auth/ (AuthGate, SocialAuthButtons)
    │   ├── map/ (LazyMap, BusinessMap, UbicacionPicker)
    │   ├── navigation/ (NavBar, RoleSwitch, BottomNav)
    │   └── ui/ (Button, Field, Badges, icons)
    └── pages/
        ├── HomePage.tsx
        ├── ExplorePage.tsx
        ├── BusinessDetailPage.tsx
        ├── MapPage.tsx
        ├── HowItWorksPage.tsx
        ├── OnboardingPage.tsx
        ├── FacilitatorDashboard.tsx
        ├── auth/ (LoginPage, RegisterPage)
        ├── business/ (DashboardPage, BusinessProfileEditor)
        └── client/ (ClientDashboard)
```

### B. Tecnologías y Dependencias
- **Librería principal:** React v19.2.0 + React DOM v19.2.0.
- **Enrutamiento:** React Router DOM v7.
- **Lenguaje:** TypeScript v5.9.
- **Estilos:** Tailwind CSS v3.4 con tipografía y paleta barrial (`brand`, `ink`, `cream`).
- **Mapas:** Leaflet v1.9 + React-Leaflet v5.
- **Formularios y Validación:** `react-hook-form` + `zod` + `@hookform/resolvers`.
- **Linter y compilador:** Oxlint v1.28 + Vite v8.2.

---

## 3. Rectificaciones Técnicas Realizadas en Esta Sesión

Durante la auditoría del código se detectaron dos puntos de inconsistencia que fueron corregidos y verificados:

### Rectificación 1: Soporte de rol Facilitador en registro backend (`backend/src/routes/auth.js`)
- **Problema encontrado:** En `backend/src/routes/auth.js`, la constante `ACCOUNT_TYPES` estaba definida únicamente como `["client", "business"]`. Cuando un usuario intentaba registrarse con el tipo de cuenta `facilitador` a través de la API, el código aplicaba un operador ternario que degradaba automáticamente su rol a `"client"`, impidiéndole acceder a las funciones de acompañamiento comunitario.
- **Solución aplicada:** Se actualizó la constante a `["client", "business", "facilitador"]` en consonancia con el enum `account_type` de PostgreSQL (`schema.sql`).
- **Verificación:** Código alineado con el esquema relacional.

### Rectificación 2: Eliminación de efecto cascada en `BusinessProfileEditor.tsx` (`frontend/`)
- **Problema encontrado:** El analizador de linter `oxlint` arrojaba advertencias de severidad `react(set-state-in-effect)` en `frontend/src/pages/business/BusinessProfileEditor.tsx`. El componente llamaba a `setPosition`, `setPhotos`, `setHours` y `setManagedBusiness` de forma síncrona dentro de `useEffect` al montar la vista, provocando renderizados en cascada que forzaban al compilador de React 19 a descartar optimizaciones automáticas. Además, existía un problema de estrechamiento de tipos en TypeScript (`TS2345`) al pasar `userId` a una clausura asíncrona interna.
- **Solución aplicada:**
  1. Se refactorizó la vista dividiéndola en dos componentes: el contenedor de carga y permisos (`BusinessProfileEditor`) y el formulario de edición (`BusinessProfileEditorForm`).
  2. El formulario se renderiza con una clave declarativa de React (`key={activeBusiness?.id ?? 'nuevo'}`). De este modo, los estados de posición, fotos y horarios se inicializan directamente en `useState` según el negocio cargado, sin necesidad de ningún efecto secundario síncrono.
  3. Se encapsuló la llamada a `loadTargetBusiness(uid: string, bid: string)` con parámetros tipados para garantizar que el compilador de TypeScript valide `uid` como cadena de texto no nula.
- **Verificación:**
  - `oxlint src`: **0 warnings, 0 errors** en los 60 archivos analizados.
  - `tsc -b`: **0 errores de tipo**.
  - `vite build`: Compilación exitosa en 7.6 segundos, generando los fragmentos listos para producción.

---

## 4. Estado de los Repositorios Git
- Ambos repositorios (`frontend` y `backend`) cuentan con las modificaciones probadas localmente y se encuentran listos para el flujo de ramas y documentación que solicite el usuario.
- No hay fugas de archivos de agentes ni del vault dentro de los repositorios de código.

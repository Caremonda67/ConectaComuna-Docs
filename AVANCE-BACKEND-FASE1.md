# Backend — avance del paso 1 (3 sept 2026)

Lo que dejamos hecho hoy del backend y lo que sigue. Va junto con `ARQUITECTURA-BACKEND.md`, que explica cómo queda la API.

## Lo que hicimos

**Base de datos (`backend/schema.sql`):**
- Al negocio (`perfiles`) le agregamos tres campos que no estaban: el usuario dueño (`auth_user_id`), la ubicación (`lat` y `lng`, para el mapa) y el nivel de verificación (`verificacion_nivel`, de 0 a 2).
- Además le pusimos unos índices a `perfiles` para que las búsquedas vayan rápido.

**Rutas de la API (`backend/src/routes/`):**
- `categorias.js` — GET /api/categorias
- `comunas.js` — GET /api/comunas
- `perfiles.js` — GET /, GET /:id, POST /, PATCH /:id
- `productos.js` — GET /, POST /, PATCH /:id, DELETE /:id
- `solicitudes.js` — POST / (público, guarda un pedido)
- `resenas.js` — POST / (público, deja una reseña, valida que sea de 1 a 5)

**Login (`backend/src/config/auth.js`):** revisa el token que manda el navegador con Supabase y nos dice quién es el usuario.

**Probado:** los archivos pasan la revisión de sintaxis y el servidor arranca y responde en `/api/salud`. Las rutas quedaron montadas y responden (con la base de datos configurada harán su trabajo completo).

## Lo que sigue

- Crear el archivo `.env` con las claves de Supabase.
- Aplicar `schema.sql` en la consola SQL de Supabase.
- Cerrar lo del facilitador y las denuncias (paso 2).
- Luego chat, rangos e insignias (paso 3).

Los pasos 2 y 3 están explicados en `ARQUITECTURA-BACKEND.md`.

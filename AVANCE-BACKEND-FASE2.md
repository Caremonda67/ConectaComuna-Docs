# Backend — avance del paso 2 (3 sept 2026)

Lo que dejamos hecho hoy del backend, enfocado en los roles y en que un facilitador pueda ayudar a registrar negocios. Va junto con `ARQUITECTURA-BACKEND.md`, que explica la API completa.

## Lo que hicimos

**Base de datos (`backend/schema.sql`):**
- Nueva tabla `usuarios`: guarda el rol de cada usuario de Supabase Auth (`emprendedor`, `facilitador` o `admin`), con su nombre. Así el login sabe quién es cada uno y qué puede hacer.

**Rutas de la API (`backend/src/routes/`):**
- `auth.js` (nuevo):
  - `POST /api/auth/registro` — crea el usuario en Supabase Auth y le asigna un rol (emprendedor o facilitador).
  - `GET /api/auth/mi-cuenta` — devuelve quién es el usuario autenticado y qué rol tiene.
- `auth.js` (config) — `usuarioDesdePeticion` ahora, además de validar el token, consulta `usuarios` y devuelve el rol.
- `perfiles.js` — un facilitador puede crear un perfil en nombre de un emprendedor (con su usuario o sin dueño aún) y editar negocios ajenos. El emprendedor sigue editando solo los propios.
- `productos.js` — el facilitador también puede agregar, editar y borrar productos de los negocios que administra.

**Probado:** los archivos pasan la revisión de sintaxis y el servidor arranca y responde en `/api/salud`. Las rutas quedaron montadas.

## Lo que sigue

- Crear el archivo `.env` con las claves reales de Supabase.
- Aplicar `schema.sql` (con la tabla `usuarios`) en la consola SQL de Supabase.
- Probar en vivo el registro con rol y la alta asistida del facilitador.
- Luego denuncias y la moderación de verificación (resto del paso 2), y al final chat, rangos e insignias (paso 3).

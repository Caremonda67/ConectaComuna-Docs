# Reconciliación de schemas — Frontend vs Backend (6 sept 2026)

Análisis creado por Antigravity comparando:
- frontend/supabase/schema.sql (lo que el frontend espera)
- backend/schema.sql (lo que el backend implementó)

**CONCLUSION: Los dos schemas son INCOMPATIBLES tal como están.**
El frontend fue diseñado pensando en conectarse directamente a Supabase (sin API Express intermedia).
El backend implementó una API REST propia con naming en español.
Hay que tomar UNA decision y alinear todo el equipo antes de seguir.

---

## Diferencias tabla por tabla

### Usuarios / Perfiles de persona

| Aspecto | Backend | Frontend |
|---------|---------|----------|
| Tabla | usuarios | profiles |
| Llave primaria | uuid ref auth.users | uuid ref auth.users (igual) |
| Nombre completo | nombre (text) | full_name (text) |
| Tipo de cuenta | rol: emprendedor/facilitador/admin | account_type: client/business |
| Teléfono | no tiene | phone (text) |
| Avatar | no tiene | avatar_url (text) |
| Barrio personal | no tiene | neighborhood (text) |
| Trigger auto-crear | NO tiene | SI: handle_new_user() |

**Impacto:** El backend no crea el perfil de usuario automáticamente al registrarse.
El frontend asume que Supabase lo hace via trigger.

---

### Negocios / Perfiles de emprendimiento

| Aspecto | Backend | Frontend |
|---------|---------|----------|
| Tabla | perfiles | businesses |
| Dueño | auth_user_id (no único: 1 usuario → N negocios) | owner_id unique (1 usuario → 1 negocio) |
| Nombre | nombre_comercial | name |
| Descripción | descripcion | description |
| Categoría | categoria_id FK → categorias | category (text slug, sin FK) |
| Ubicación | lat numeric(10,7), lng numeric(10,7) | lat double precision, lng double precision |
| Fotos | foto_url (text, una sola) | photos (text[], array de varias) |
| Horarios | NO tiene | hours (jsonb) |
| WhatsApp | whatsapp (text) | whatsapp (text) — IGUAL |
| RRSS | instagram, facebook | NO tiene |
| Reputación | NO tiene (calculada en cliente/query) | rating_avg, rating_count, completed_orders (triggers) |
| Verificación | verificacion_nivel (0/1/2) | NO tiene |
| Activo | NO tiene | is_active (boolean) |
| Atiende pedidos | atiende_pedidos (boolean) | NO tiene |
| Vende mayor | (en productos.precio_mayor) | NO tiene en businesses |
| Timestamps | creado_en, actualizado_en | created_at |
| RLS | NO tiene | SI tiene (public select, owner write) |

**Impacto:** Diferencias críticas en cardinalidad (1→N vs 1→1), fotos (string vs array),
reputación (sin triggers vs con triggers), categorías (FK vs slug libre).

---

### Productos / Items

| Aspecto | Backend | Frontend |
|---------|---------|----------|
| Tabla | productos | NO tiene tabla separada |
| Relación | perfil_id FK | los productos no existen como entidad en el frontend actual |
| Precio mayor | precio_mayor (numeric) | NO tiene |

**Impacto:** El frontend actual NO modela productos como entidad separada.
Asume que el negocio tiene servicios directamente. Diferencia conceptual grande.

---

### Pedidos

| Aspecto | Backend | Frontend |
|---------|---------|----------|
| Tabla | solicitudes | orders |
| Referencia | producto_id | business_id (sin producto específico) |
| Cliente | nombre + telefono (anónimo, sin auth) | client_id FK profiles (requiere auth) |
| Estado | nueva/atendida/cerrada | pending/accepted/in_progress/completed/cancelled |
| Título | NO tiene | title (text) |
| Precio estimado | NO tiene | price_estimate |
| Trigger | NO tiene | bump_completed_orders() |

**Impacto:** El backend permite pedidos anónimos; el frontend requiere cuenta.
Los estados son distintos e incompatibles.

---

### Reseñas

| Aspecto | Backend | Frontend |
|---------|---------|----------|
| Tabla | resenas | reviews |
| Referencia | perfil_id | order_id UNIQUE + business_id (1 reseña por pedido) |
| Autor | texto libre (anónimo) | client_id FK profiles (requiere auth) |
| Restricción | ninguna (cualquiera reseña) | solo si existe pedido completado propio |
| Trigger reputación | NO tiene | SI: refresh_business_rating() |

**Impacto:** Backend sin seguridad para reseñas (cualquiera puede inflar); frontend robusto.

---

### Lo que el backend tiene y el frontend NO necesita

- comunas (tabla de referencia geográfica)
- categorias (tabla con FK) — frontend usa slugs libres
- facilitador como rol
- verificacion_nivel

### Lo que el frontend necesita y el backend NO tiene

- trigger handle_new_user()
- RLS en todas las tablas
- hours (jsonb en businesses)
- photos como array
- rating_avg/count como columnas con triggers
- orders con estados granulares
- reviews ligadas a orders

---

## DECISION QUE HAY QUE TOMAR

**Opcion A (recomendada): adoptar el schema del frontend como schema definitivo.**
- El frontend ya está construido sobre él y tiene RLS, triggers y lógica de seguridad robusta.
- El backend adapta sus rutas para hablar con las tablas del frontend.
- Se aplica frontend/supabase/schema.sql en Supabase.
- El backend Express actúa como proxy/middleware; el frontend puede hablar directo a Supabase
  para operaciones simples (Supabase client SDK) y al backend Express para lógica compleja.

**Opcion B: mantener el schema del backend y reescribir el frontend.**
- Implica reescribir todos los servicios del frontend, los tipos, y los componentes que tocan datos.
- Coste alto: el frontend está muy avanzado.
- NO recomendada.

**Opcion C: schema híbrido.**
- Fusionar lo que tiene solo el backend (comunas, facilitador, verificacion_nivel, venta al por mayor)
  al schema del frontend como tablas/columnas adicionales.
- Requiere agregar a businesses: verificacion_nivel, vende_por_mayor, comunas FK.
- Requiere agregar a profiles: rol (para facilitador y admin).
- El resto del schema del frontend se mantiene.
- Viable si el equipo quiere conservar funcionalidades del plan maestro (facilitador, verificación).

---

## ACCIONES CONCRETAS (según decision)

Si eligen Opcion A (schema frontend):
1. Aplicar frontend/supabase/schema.sql en Supabase (consola SQL).
2. Reescribir backend/schema.sql para que sea idéntico al del frontend.
3. Actualizar rutas del backend (perfiles → businesses, resenas → reviews, etc.).
4. Agregar RLS al backend o documentar que la seguridad la maneja Supabase directo.

Si eligen Opcion C (híbrido):
1. Extender frontend/supabase/schema.sql con:
   - columna businesses.verificacion_nivel
   - columna businesses.vende_por_mayor
   - columna profiles.rol (client/business/facilitador/admin)
   - tabla comunas
2. Actualizar types/index.ts del frontend con esos campos nuevos.
3. Actualizar rutas del backend para usar los nombres del frontend.

---

Nota: la decision afecta a las 5 personas del equipo. Definir ANTES de que alguien más codifique.

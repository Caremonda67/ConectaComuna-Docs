# Frontend — estado real (6 sept 2026)

Nota creada por Antigravity tras hacer `git pull origin main` del repo ConectaComuna-Frontend.
El frontend local anterior (3 páginas placeholder) fue descartado y reemplazado por este.

## Commit activo

2358e76 — "Se crea un FrontEnd desde cero como base para que se trabaje el proyecto"

## Estructura real de src/

`
src/
  components/
    business/   BusinessCard.tsx, BusinessFiltersBar.tsx
    layout/     AppLayout.tsx, Logo.tsx, ProtectedRoute.tsx, SiteFooter.tsx
    map/        BusinessMap.tsx, LazyMap.tsx
    orders/     OrderCard.tsx
    ui/         Badges.tsx, Button.tsx, CategoryGlyph.tsx, Field.tsx,
                icons.ts, Rating.tsx, Skeleton.tsx, States.tsx
  context/      AuthContext.tsx
  data/         categories.ts, demoData.ts
  hooks/        useAsync.ts, useAuth.ts, useGeolocation.ts
  lib/          env.ts, supabase.ts, utils.ts
  main.tsx / index.css / router.tsx
  pages/
    HomePage, ExplorePage, MapPage, BusinessDetailPage,
    HowItWorksPage, DashboardPage
    auth/       LoginPage, RegisterPage
    business/   BusinessDashboard, BusinessProfileEditor
    client/     ClientDashboard
  services/
    authService, businessService, orderService, profileService, demoBackend
  types/        index.ts
supabase/
  schema.sql    (contrato BD que espera el frontend)
`

## Rutas definidas (router.tsx)

| Ruta | Página | Protegida |
|------|--------|-----------|
| / | HomePage | No |
| /explorar | ExplorePage | No |
| /mapa | MapPage | No |
| /negocio/:id | BusinessDetailPage | No |
| /como-funciona | HowItWorksPage | No |
| /entrar | LoginPage | No |
| /registro | RegisterPage | No |
| /panel | DashboardPage | Si |
| /panel/negocio | BusinessProfileEditor | Si + requireBusiness |

## Tipos del dominio (src/types/index.ts)

- AccountType: 'client' | 'business'
- ActiveRole: 'client' | 'business' (estado UI, no permiso)
- OrderStatus: pending | accepted | in_progress | completed | cancelled
- CategorySlug: costura, manicure, cerrajeria, ropa, ambulante, belleza, comida, domicilios, tecnologia, construccion, otros
- Interfaces: Profile, Business, Order, Review, Badge, BusinessFilters, BusinessWithDistance

## Modo demo integrado

Sin .env la app corre con datos locales (localStorage).
Login demo: user-cliente@demo.co o user-negocio@demo.co + cualquier contrasena.
Servicios detectan credenciales Supabase y cambian de adaptador automaticamente.

## Sistema de diseno (tokens en src/index.css)

- cream-50 #F9F8F2 — fondo de la app
- ink-900 #1A1A1A — texto principal
- brand-100 #CFE8C6 — acentos, chips, badges
- brand-500 #6FB257 — accion primaria (botones, estrellas)

Decisiones: sin sombras, botones rounded-full, iconografia lucide centralizada en icons.ts,
rojo solo en errores y pedidos cancelados.

## Decisiones tecnicas clave

- Capa de servicios como frontera: componentes no conocen Supabase
- Filtrado en servidor con Supabase (lat/lng + Haversine)
- Filtros en URL con useSearchParams (compartible por WhatsApp)
- Sin estado global pesado: solo sesion en Context; Zustand y TanStack Query instalados pero sin usar aun
- Code splitting por ruta (lazy); Leaflet lazy ~150 KB
- Rendimiento pensado para conexiones lentas y gama baja

## Contrato con el backend (supabase/schema.sql del frontend)

- Tabla profiles con account_type (trigger handle_new_user() siembra el perfil)
- rating_avg, rating_count, completed_orders calculados por triggers (no en cliente)
- Resenas solo si existe pedido propio en estado completed
- Storage bucket: business-photos/<uid>/<archivo>

## DIFERENCIAS CRITICAS con el backend actual

El backend (backend/schema.sql) usa nombres distintos al frontend (supabase/schema.sql).
Hay que reconciliar antes de conectar:

| Concepto | Backend usa | Frontend espera |
|----------|-------------|-----------------|
| Perfil de negocio | perfiles | businesses (probablemente) |
| Dueno del negocio | auth_user_id | owner_id |
| Tipo de cuenta | rol en tabla usuarios | account_type en profiles |
| Imagenes | foto_url (string) | photos (array) |

PENDIENTE URGENTE: revisar supabase/schema.sql del frontend y reconciliar con backend/schema.sql.
Este es el trabajo mas critico antes de conectar las dos capas.

## Lo que falta

- .env con claves reales de Supabase
- Reconciliar schema del frontend con el del backend
- node_modules ya instalados

## Archivos extra del repo

- supabase/schema.sql — contrato BD del frontend
- vercel.json — reescribe rutas a index.html
- .oxlintrc.json — config del linter (oxlint)
- dist/ — build commiteado (revisar si debe estar en .gitignore)
- **6 sept 2026**: Se reconciliaron los schemas. Se eligió la Opción A: el schema del frontend es la única fuente de verdad. El backend fue reescrito completamente para usar los nombres de tabla del frontend (businesses, orders, reviews, profiles). Las rutas Express también fueron renombradas.

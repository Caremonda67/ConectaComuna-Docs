# Plan: Búsqueda de negocios por ubicación (estilo Rappi)

**Fecha:** 7 sept 2026
**Sprint:** 1
**Origen:** Decisión del coordinador tras revisar cómo busca Rappi (elegir tu dirección u otra, filtrar por cercanía).

## Objetivo

Que el usuario cali (móvil primero, gama baja) pueda ver los oficios de la comuna ordenados por cercanía a un punto que él elige: su ubicación por GPS, una dirección que escribe, o un punto que toca en el mapa. Y que pueda guardar direcciones frecuentes (como los domicilios de Rappi) para elegirlas luego con un toque.

## Decisiones tomadas (7 sept)

1. **Geocoder en demo = barrios + pin en el mapa.** El buscador de dirección en modo demo resuelve contra un catálogo local de barrios/direcciones típicas de la comuna (sin red ni APIs). En modo Supabase real se conecta el geocoder abierto de OpenStreetMap (Nominatim). Elegir el punto en el mapa queda siempre como opción de respaldo.
2. **Se integra en `/explorar` y `/mapa`.** Son las dos pantallas que ya filtran por centro + radio. La portada `/` queda fuera por ahora.
3. **Direcciones guardadas van ahora mismo.** Tabla nueva en español `direcciones_usuario` (REGLA-IDIOMA-BD) con CRUD en el panel del cliente.

## Cómo encaja con lo que ya existe

La app ya trae lo difícil:

- `Business` tiene `lat`, `lng`, `address`, `neighborhood`.
- `businessService.search()` ordena por distancia y filtra por centro + radio (Haversine) en modo demo y Supabase.
- `BusinessFilters.center` ya existe; solo hay que darle el punto que elige el usuario.
- El mapa Leaflet (`LazyMap` / `BusinessMap`) ya tiene modo "pickable" para elegir coordenadas.
- `useGeolocation()` ya resuelve el GPS con degradación al centro de la comuna.

Lo nuevo:
- Un selector de ubicación con tres modos (GPS / buscar barrio-dirección / tocar el mapa).
- Servicio de geocodificación (demo local + OSM real).
- Tabla y CRUD de `direcciones_usuario`.

## Estado (8 sept, madrugada) — completo

Las fases 1, 2 y 3 quedaron cerradas y verificadas en el navegador (modo demo). Evidencia en [[EVIDENCIA-Busqueda-por-Ubicacion]].

**Rama Supabase real probada (8 sept):** aplicamos el schema y los grants de lectura anónima en el proyecto real (`xzsqdyluhawtpoxbldak`), sembramos usuarios y negocios con la service role, y probamos el ciclo completo con la app en modo real: login de Luisa, listado de sus direcciones, crear "Casa de la abuela" (persistida por su token con RLS), borrarla y ver /explorar con los 6 negocios ordenados por cercanía. Evidencia en [[EVIDENCIA-Rama-Real-Supabase]]. Pendiente solo GPS de verdad en celular y rotar llaves de Supabase antes de la defensa.

El picker quedó como un panel inline reutilizable que devuelve `{ etiqueta, center, origen }` y no persiste nada; la persistencia (localStorage `conectacomuna.ubicacion`) vive en `lib/ubicacion.ts` y la usan Explore y Mapa. En `demoBackend` además corregimos `readDb()` para que rellene con el seed los campos que faltan en sesiones viejas (`direcciones` no existía y rompía `listarPorUsuario`).

## Cómo encaja (contexto original)

La búsqueda por formulario ya se integra al hacer:
1. `UbicacionPicker` devuelve `{ etiqueta, center, origen }`.
2. Ese `center` alimenta `BusinessFilters.center` en Explore y en el Mapa (mismo patrón que hoy usa `position`).
3. El radio de búsqueda y el orden por distancia siguen igual.

## Tareas

### Fase 1 - Datos y servicios
- [x] **T1: Tipo `DireccionUsuario` + tabla SQL + colección demo.**
  - `types/index.ts`: interface con campos en español (`usuario_id`, `etiqueta`, `direccion_texto`, `barrio`, `lat`, `lng`, `creado_en`).
  - `frontend/supabase/schema.sql`: tabla `direcciones_usuario` + RLS con 4 políticas `*_propias` (select/insert/update/delete con `auth.uid() = usuario_id`).
  - `demoBackend.ts`: colección `direcciones` en `DemoDb` + seed.
  - `demoData.ts`: 2-3 direcciones demo para el cliente Luisa.
- [x] **T2: Servicio `direccionService` + geocoder.**
  - `direccionService.ts`: `listarPorUsuario`, `crear`, `actualizar`, `eliminar` (demo + Supabase, siguiendo el patrón de `orderService`).
  - Catálogo local de barrios/direcciones de la comuna para el geocoder demo (`geocodificar()` → `{etiqueta, center}[]`), y rama real con Nominatim de OSM.

### Checkpoint Fase 1
- [x] Typecheck y lint pasan.
- [x] `schema.sql` legible por cualquier compa (y por Antigravity).

### Fase 2 - Selector de ubicación
- [x] **T3: Componente `UbicacionPicker`** (reutilizable).
  - Tres modos: "Usar mi ubicación" (usa `useGeolocation`), "Buscar barrio o dirección" (autocompletado con el geocoder), "Elegir en el mapa" (`LazyMap` pickable).
  - Retorna `{ etiqueta, center, origen }` y muestra la etiqueta elegida.
  - Accesible (botones con label, lista de sugerencias con `role="listbox"` si aplica), sin "AI aesthetic".
- [x] **T4: Integrar en `/explorar`.**
  - El estado del `center` pasa de `useGeolocation()` a la ubicación elegida (GPS sigue disponible dentro del picker).
  - Se muestra la etiqueta ("Tu ubicación" o "Calle 48 #40-12, Antonio Nariño") junto a la barra de filtros.
- [x] **T5: Integrar en `/mapa`.**
  - El mapa se re-centra a la ubicación elegida y los resultados siguen el mismo `center`.

### Checkpoint Fase 2
- [x] En modo demo: escribir "rr" en el buscador sugiere "El Rodeo" y al elegirlo el listado se reordena por cercanía a ese punto. (Probado escribiendo "rodeo", no "rr", y reordena igual.)
- [x] Elegir en el mapa funciona y el marcador del usuario se ve en el punto tocado.

### Fase 3 - Direcciones guardadas
- [x] **T6: Sección "Mis direcciones" en el panel del cliente.**
  - Nueva `<section>` en `ClientDashboard.tsx` (patrón matching de las otras secciones).
  - Lista con etiqueta + dirección + barrio, botones para usar (centra la búsqueda), eliminar.
  - Formulario inline para crear/editarla usando `Field` + `UbicacionPicker` para capturar coordenadas.
  - Estados de carga/vacío/error con `useAsync` + `States`.

### Checkpoint Fase 3 / completo
- [x] Typecheck, lint y build de producción.
- [x] Flujo end-to-end en navegador (demo): guardar dirección, volver a `/explorar`, elegirla y ver resultados ordenados por cercanía.
- [x] Documentar en el vault (avance + evidencia para el instructor).

## Archivos que se tocan

- `frontend/src/types/index.ts`
- `frontend/supabase/schema.sql`
- `frontend/src/services/demoBackend.ts`
- `frontend/src/data/demoData.ts`
- `frontend/src/services/direccionService.ts` (nuevo)
- `frontend/src/components/location/UbicacionPicker.tsx` + sugerencias (nuevo)
- `frontend/src/pages/ExplorePage.tsx`
- `frontend/src/pages/MapPage.tsx`
- `frontend/src/pages/client/ClientDashboard.tsx`
- `frontend/src/hooks/useGeolocation.ts` (leve, o se consumen sus datos desde el picker)

## Riesgos

- **Geocoder en demo impreciso**: está bien, es simulado; el pin en el mapa es el respaldo exacto.
- **Nominatim en producción**: sin API key, limita peticiones (1 req/s). Para la entrega demo no se usa, y en producción se puede cambiar por Mapbox más adelante.
- **`DireccionUsuario` con nombres en español**: contrasta con los campos en inglés de las tablas viejas; es intencional (REGLA-IDIOMA-BD).

## Abierto

- ¿Las direcciones se comparten entre roles (un facilitador también es cliente)? Sí; la sección vive en el dashboard de cliente y el usuario dual la ve igual.
# Evidencia - Búsqueda por ubicación (estilo Rappi)

**Fecha:** 7 sept 2026 (noche)
**Sprint:** 1
**Módulo:** Explorar / Mapa / Panel del cliente

## Qué se hizo

Cerramos el plan de búsqueda por ubicación de punta a punta. El usuario ahora elige desde dónde buscar en `/explorar` y `/mapa` por tres vías: su ubicación con GPS, un barrio o dirección que escribe, o un punto que toca en el mapa. Aparte, puede guardar direcciones frecuentes en el panel del cliente (como los domicilios de Rappi) y elegirlas después con un toque. Las seis tareas del plan quedaron hechas.

## Cómo se hizo (técnico)

- **`UbicacionPicker.tsx`**, panel inline que se abre desde el botón de la etiqueta (sin modal, no existe uno en el proyecto). Tiene tres modos: "Usar mi ubicación" (lee `useGeolocation`), "Buscar barrio o dirección" (autocompletado con 250 ms de debounce y `role="listbox"` para las sugerencias) y "Elegir en el mapa" (Leaflet `LazyMap` pickable con "Usar este punto"). Devuelve `{ etiqueta, center, origen }`.
- El picker es puro: no persiste nada. Las páginas guardan la elección en localStorage (clave `conectacomuna.ubicacion`) a través de `lib/ubicacion.ts`; por eso `/mapa` conserva lo elegido en `/explorar` y el cierre de sesión no lo borra.
- El buscador resuelve contra un catálogo local de barrios de la comuna en modo demo (sin red) y contra Nominatim de OSM en modo real. Elegir el punto en el mapa quedó como respaldo exacto.
- **`direccionService.ts`** con las cinco llamadas (listar, crear, actualizar, eliminar, geocodificar), cada una con rama demo (localStorage) y real (Supabase). La tabla nueva `direcciones_usuario` va en español según REGLA-IDIOMA-BD, con 4 políticas RLS (`*_propias`).
- En `demoBackend` además arreglamos `readDb`: ahora rellena con el seed los campos que faltan en sesiones viejas guardadas (faltaba `direcciones` y reventaba el `filter`).

## Cómo funciona (paso a paso)

1. En `/explorar` el botón muestra la etiqueta actual ("Casa", "El Rodeo", "Tu ubicación"...). Al abrirlo se ven las tres opciones.
2. Escribiendo "rodeo" salen las sugerencias "El Rodeo" e "Iglesia El Rodeo". Al elegir una, el listado se reordena por cercanía a ese punto. También se puede tocar el mapa y confirmar con "Usar este punto".
3. En `/panel`, la sección "Mis direcciones" lista las guardadas con botones Usar y Borrar, y un formulario para crear más (nombre + el mismo picker para las coordenadas). Probamos crear "Casa de la abuela → Parque Antonio Nariño" y funciona; Borrar pide confirmación.
4. "Usar" salta a `/explorar` y centra la búsqueda en esa dirección.

## Estado actual

Completo en modo demo y verificado en el navegador. El orden de resultados coincide con las distancias reales: desde El Rodeo (3.4401, -76.5209) quedan Wendy a 302 m, Costuras a 419 m, Cerrajería a 652 m y Uñas de Kelly a 667 m. Typecheck, lint y build de producción en verde; sin errores de consola en las pruebas.

Pendiente para más adelante: probar la rama Supabase real (requiere configurar `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY`) y el GPS en un celular de verdad, que en la laptop del equipo sin permiso de ubicación solo se simuló.
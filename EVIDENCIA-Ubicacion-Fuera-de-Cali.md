# Evidencia: la app deja de asumir que todos estamos en Cali

Fecha: 28 de octubre de 2026.
Rama frontend: `fix/ubicacion-fuera-de-cali`. Rama backend: `feat/moderacion-reportes`.
Motivo: faltan 16 días para la final presencial en Bogotá y la app trataba a cualquier usuario como si estuviera en Cali.

## De dónde salió

Probando el selector de ubicación, marqué un punto del mapa en Bogotá y la app siguió mostrando Cali. Rastreando ese síntoma apareció un patrón completo, no un fallo suelto: el supuesto "el usuario está en Cali" estaba repartido por todo el código.

El detalle completo quedó en [[AUDITORIA-FLUJOS-PREFINAL]].

## Qué se arregló

### El geocodificador estaba amarrado a Cali

`geocodificar` mandaba a Nominatim un `viewbox` con el recuadro de Cali y `bounded=1`, que le dice al servicio que no devuelva nada de fuera. Por eso buscar "Chapinero" daba cero resultados. Ahora la consulta se acota solo a Colombia con `countrycodes=co`.

Se agregó `reverseGeocodificar`, que faltaba: convierte unas coordenadas en "Barrio, Ciudad". Se usa en los tres puntos por donde entra una ubicación: el GPS, el punto que marcas en el mapa y el GPS del asistente.

### Tres respuestas distintas a la misma pregunta

Explorar y Mapa decidían si estabas fuera del piloto con un radio de 35 km. El asistente lo decidía con una caja de latitud y longitud. En los bordes (Yumbo, Jamundí, Palmira) se contradecían: el mapa decía que no hay nada cerca y el asistente respondía como si estuvieras en el barrio.

Ahora los tres preguntan a `lib/zonaPiloto.ts`, que es el único sitio donde vive el criterio.

### El GPS arrancaba creyendo que estabas en Cali

`useGeolocation` inicializaba la posición en el centro de la comuna, así que un permiso negado era indistinguible de una lectura buena. Ahora expone `lectura`, que vale `null` hasta que el GPS responde, y `position`, que mantiene el respaldo para las pantallas que solo necesitan un centro de búsqueda.

### Un barrio nombrado al pasar te cambiaba de ciudad

`detectarBarrioEnTexto` buscaba con `includes` suelto sobre el mensaje, y su resultado pesaba más que la ubicación que el usuario ya había elegido. Dos consecuencias: "vallado" encajaba dentro de "amurallado", y escribir "mi prima vive en El Rodeo" reubicaba en Cali a alguien que estaba en Bogotá.

Ahora la búsqueda respeta límites de palabra, y el barrio del texto solo manda si la persona declara que está ahí ("estoy en", "vivo en", "mi barrio") o si todavía no ha elegido ubicación.

### El localizador escribía un barrio ajeno en tu perfil

Cuando Nominatim no respondía, `useNeighborhoodLocator` rellenaba el campo con "Antonio Nariño". Alguien registrándose desde Bogotá terminaba con un barrio de Cali guardado. Ahora se le pide que lo escriba.

### Coordenadas sueltas en la tarjeta de negocio

`BusinessCard` comparaba contra `3.4372` y `-76.5225` escritos a mano para decidir si mostrar "Ubicación por confirmar". Si alguien cambiaba `COMUNA_CENTER`, esa comparación quedaba huérfana sin que nadie lo notara. Ahora lee la constante.

### La política de privacidad decía algo que ya no era cierto

El texto afirmaba que las consultas al geocodificador se acotan "al perímetro geográfico de Cali (viewbox local)". Al quitar el recuadro eso dejó de ser verdad, y es un documento legal que el jurado puede abrir. Se corrigió para que describa lo que el código hace de verdad: acotar a Colombia.

## Reportes comunitarios: dejaron de ser humo

Reportar un negocio no hacía nada contra la base. El umbral de dos reportes vivía en `localStorage`, así que cada navegador contaba los suyos y el negocio nunca quedaba marcado para los demás vecinos. Además la función que leía esa marca empieza con `if (!isDemoMode) return`, y la app corre en modo real.

En el camino aparecieron dos fallos de base que nadie había visto:

- La columna `report_count` no existe. El frontend la leía y siempre venía indefinida.
- El frontend marca los negocios señalados como `under_review` en siete sitios, pero el `check` de `verification_status` solo admitía cuatro valores y ese no estaba. Cualquier escritura habría reventado con error 23514.

La migración `backend/migraciones/2026-10-28-conteo-de-reportes.sql` agrega la columna, extiende el check y crea el trigger `refresh_business_reports`, que cuenta los reportes pendientes de cada negocio. Dos lo dejan bajo observación; revisarlos o descartarlos lo liberan, salvo que ya estuviera verificado.

**Pendiente: esa migración todavía no se ha aplicado en Supabase.** Hay que pegarla en la consola SQL.

## Bandeja de moderación

`GET /api/reports` y `PATCH /api/reports/:id` existían desde septiembre, validan rol facilitador y guardan quién moderó. Ninguna pantalla los llamaba.

Se agregó `components/trust/ModeracionReportesPanel.tsx` al panel del facilitador: lista los reportes pendientes con el motivo y la descripción del vecino, permite confirmarlos o descartarlos con una nota de campo, y guarda los resueltos en un desplegable.

## Comprobaciones

- `tsc -b` limpio.
- `oxlint` sin warnings.
- `vite build` correcto.
- Falta la prueba en navegador con el GPS puesto en Bogotá y con el GPS negado. Es la que importa de verdad y hay que hacerla antes de viajar.

## Commits

| Rama | Commit | Qué |
|---|---|---|
| `fix/ubicacion-fuera-de-cali` | `5f86930` | Geocodificación sin recuadro y reverse geocoding |
| | `a73c84c` | Criterio único de zona piloto |
| | `bbf2f36` | Bandeja de moderación y conteo real |
| | `0824838` | Barrio mencionado de paso, localizador y privacidad |
| `feat/moderacion-reportes` | `9473f47` | Migración de conteo de reportes |

Ninguno se ha subido todavía. Falta revisarlos y abrir los dos pull requests.

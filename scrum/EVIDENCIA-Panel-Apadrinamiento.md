# Evidencia - Panel de apadrinamiento (dueño del negocio)

**Fecha:** 7 sept 2026
**Sprint:** 1
**Módulo:** Facilitadores

## Qué se hizo
Cerramos el flujo de apadrinamiento por el lado del dueño. Ahora, cuando un facilitador solicita apadrinar un negocio, el dueño recibe la solicitud en su panel de negocios con los datos del solicitante (nombre y teléfono) y puede aprobarla o rechazarla con un clic. Antes el flujo se quedaba en el envío de la solicitud; esta pantalla era el eslabón que faltaba.

## Cómo se hizo (técnico)
- Frontend React con TypeScript. Sin backend Express: la escritura va directo a Supabase con las políticas RLS de `facilitadores_negocio` (decisión tomada para no duplicar la lógica).
- `facilitadorService.ts` expone cinco operaciones: `getNegociosVinculados`, `getSolicitudesPendientes`, `solicitarVinculacion`, `responderSolicitud` y `getVinculacionesDelNegocio`. Cada una tiene dos ramas: demo (localStorage con `demoBackend`) y real (Supabase con join por clave foránea para traer el nombre del facilitador y del negocio).
- En modo demo, `demoBackend` guarda las vinculaciones y `demoData` siembra una solicitud pendiente de prueba (negocio `biz-1`, facilitador `user-facilitador`).
- La UI es el componente `ApadrinamientoPanel` dentro de `BusinessDashboard.tsx`: lista las solicitudes pendientes y responde con Aprobar/Rechazar.

## Cómo funciona (paso a paso)
1. Un vecino con cuenta de facilitador entra a un negocio y solicita apadrinarlo.
2. El dueño entra a `/panel`. En "Solicitudes de apadrinamiento" ve la petición con el nombre y teléfono del facilitador.
3. Si la aprueba, la solicitud sale de la lista; el facilitador pasa a ver el negocio en su panel con estado "Acceso concedido" y botones para editar el perfil.
4. Si la rechaza, la solicitud queda descartada (en demo, si vuelve a solicitar, entra de nuevo a la lista para la próxima prueba).

## Estado actual
El flujo en modo demo está completo y probado en el navegador. Falta probar contra Supabase real (los llamados están escritos, requieren configurar `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY`). Pendiente también un botón para que el dueño revoque el acceso cuando quiera.
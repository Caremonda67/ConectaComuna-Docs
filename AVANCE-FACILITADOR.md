# Avance Frontend — Rol Facilitador y Context Switching

**Fecha:** 6 sept 2026
**Responsable:** Luis Santiago (Coordinador)
**Sprint:** 1

Terminamos de implementar la lógica visual para separar los sombreros de "Cliente" y "Facilitador" en el frontend. Tuvimos unas charlas de diseño para evitar que el usuario cometiera errores (como intentar pedir un servicio a nombre de su negocio o apadrinar por error) y decidimos forzar un modelo de roles estrictos.

## Lo que hicimos

1. **Registro actualizado:**
   Modificamos `RegisterPage.tsx` para agregar oficialmente la tarjeta de "Facilitador (Apoyo a negocios)". Ahora, al entrar por esa opción, el usuario es redirigido de inmediato a su propio dashboard.

2. **Doble rol en el Header (`AppLayout.tsx` y `AuthContext.tsx`):**
   Vimos que un facilitador también tiene derecho a comprar cosas (pedir servicios para él mismo). Para evitar fricciones y errores de permisos, extendimos el toggle de la barra superior.
   - Si eres negocio: ves el botón [Cliente | Negocio].
   - Si eres facilitador: ves el botón [Cliente | Facilitador].
   Al hacer clic, cambias tu `activeRole` a nivel global en la app.

3. **Restricción de botones (`BusinessDetailPage.tsx`):**
   Este fue el cambio más importante de usabilidad. Si el usuario no tiene el rol activo correcto, la UI lo frena:
   - Rol `client` activo: Solo ve el botón de "Solicitar servicio".
   - Rol `facilitador` activo: Solo ve el botón de "Apadrinar (Administrar)".
   - Rol `business` activo: Ve un texto indicándole que debe cambiar a "Cliente" en el menú para poder comprar. Esto ayuda a que la gente no mezcle contextos de trabajo y consumo.

## Corrección de errores
Corrimos el linter y TypeScript. Encontramos que el botón de Apadrinar asumía ciegamente que `userId` existía. Le pusimos una guardia de seguridad para evitar caídas (crashes) en caso de que la sesión expire justo antes del clic.

## 7 sept: el dueño ya aprueba o rechaza

Construimos la pantalla que faltaba. El dueño ya ve las solicitudes de apadrinamiento en su panel (`/panel`), sección "Solicitudes de apadrinamiento". Cada solicitud muestra el nombre del facilitador y su teléfono, con botones Aprobar y Rechazar.

Técnicamente no pasamos por el backend Express: va directo a Supabase con las políticas RLS de la tabla `facilitadores_negocio`, como habíamos decidido. El servicio quedó en `frontend/src/services/facilitadorService.ts`, con cinco métodos: listar negocios vinculados, listar solicitudes pendientes, solicitar vinculación, responder la solicitud y el historial de vinculaciones del negocio. Cada método tiene rama demo (localStorage) y rama real (Supabase con join por FK).

En la base demo sembramos la vinculación `vin-1` (negocio `biz-1` con el facilitador `user-facilitador`) y el perfil de Don Ricardo Salazar. El componente `ApadrinamientoPanel` quedó dentro de `BusinessDashboard.tsx`.

Verificado en el navegador en modo demo: Doña Marta aprobó la solicitud de Don Ricardo y al entrar el facilitador en su panel el negocio aparece con "Acceso concedido". El estado quedó guardado en localStorage. TypeScript, linter y build de producción pasan.

### Errores corregidos hoy
- El perfil del facilitador no se había sembrado en la base local; el panel mostraba "Vecino facilitador" en vez del nombre. Al regenerar el seed aparece Don Ricardo Salazar completo.
- La primera inserción en `demoData.ts` no aplicó por la mezcla de saltos de línea CRLF/LF. Normalizamos los saltos antes de reemplazar.

## Próximos pasos
- Probar la escritura real contra Supabase cuando configuremos `VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY` en el frontend.
- Falta el botón para que el dueño revoque el apadrinamiento cuando lo necesite.
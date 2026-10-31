# Evidencia - Rama Supabase real de la ubicación

**Fecha:** 7 sept 2026 (madrugada del 8)
**Sprint:** 1
**Módulo:** Explorar / Mapa / Panel del cliente / Base de datos

## Qué se hizo

La parte que dejamos pendiente en la evidencia anterior ya está. La búsqueda por ubicación y las direcciones guardadas ya no corren contra el simulacro local: quedaron corriendo contra la base de datos real de Supabase (proyecto `xzsqdyluhawtpoxbldak`), con la app en modo real (`VITE_SUPABASE_URL` y `VITE_SUPABASE_ANON_KEY` puestas en `frontend/.env`). Probamos en el navegador el ciclo completo con datos de verdad y quedó funcionando.

## Cómo se hizo (técnico)

- Aplicamos el `schema.sql` (que hasta hoy nunca se había ejecutado en el proyecto de Supabase; la BD solo tenía las tablas viejas `categorias`, `productos`, `comunas`, `usuarios`, `perfiles`...). Quedaron creadas `profiles`, `businesses`, `orders`, `reviews`, `facilitadores_negocio` y `direcciones_usuario`, con sus enums (`account_type`, `order_status`), triggers (`handle_new_user`, `refresh_business_rating`, `bump_completed_orders`) y el bucket `business-photos`.
- El proyecto venía con RLS y el rol `anon` sin permisos: el API devolvía 401 aunque la llave fuera válida. Añadimos los `grant` en `frontend/supabase/acceso-cliente.sql` (select a `anon` sobre `profiles`/`businesses`/`reviews`, y manejo completo a `authenticated`/`service_role` sobre las seis tablas). Con eso el API de PostgREST con la llave anónima devuelve 200.
- No hay `DATABASE_URL` en el repo y la conexión directa (`db.<ref>.supabase.co`) no se puede usar desde este equipo: solo publica IPv6 y esta red no tiene. Entonces nos conectamos por el pooler de transacciones (`aws-0-us-west-2.pooler.supabase.com:6543?pgbouncer=true`, usuario `postgres.<ref>`). Primero fallaron dos contraseñas (el pooler ni siquiera estaba apuntando al puerto correcto); con los datos del panel *Session pooler* quedó.
- Sembramos datos con la service role key vía REST. A los usuarios los creamos por la API de auth con `email_confirm: true` porque el flujo de registro normal se bloqueó por límite de correos (429 `over_email_send_rate_limit`). El trigger `handle_new_user` crea el `profile` de cada uno. Después insertamos los seis negocios demo, dos direcciones de Luisa y una orden completada con su reseña.

## Cómo funciona (paso a paso)

1. Entramos a la app en `http://localhost:5174` (modo real, sin aviso de demo) y la portada lista los negocios que viven en la BD: Costuras Doña Marta aparece con "5.0 (1)" porque el trigger recalculó el promedio con la reseña real, y con la distancia desde la dirección elegida ("204 m" desde la ubicación por defecto).
2. Iniciamos sesión con el usuario real `user-cliente@demo.co` (Luisa Martínez). El `/panel` carga sus datos de la tabla `profiles` (nombre, celular, barrio, fecha de registro) y sus dos direcciones sembradas (Casa y Trabajo), cada una con sus botones Usar y Borrar.
3. Creamos una dirección nueva por el formulario: nombre "Casa de la abuela" y, en el picker, buscamos "Antonio Nariño". El buscador resolvió contra Nominatim (resolvió el barrio en Comuna 16 de Cali, lo guardó con coordenadas 3.4144, -76.5054). El registro apareció en la lista y quedó en la tabla `direcciones_usuario` creado por el token del usuario (la política RLS `direcciones_insert_propias` exige `auth.uid() = usuario_id`, y aplicó).
4. La borramos con su confirmación. Desapareció de la pantalla y de la tabla: quedaron solo Casa y Trabajo.
5. En `/explorar` el listado mostró "6 resultados", todos los negocios reales ordenados por cercanía desde la dirección Casa (Wendy 300 m, Costuras 419 m, Cerrajería 653 m, Uñas de Kelly 666 m, Cholados 1.0 km, TecnoFix 1.1 km), igual que los cálculos de Haversine del simulacro.

Sin errores en consola en toda la prueba.

## Estado actual

Completo. La rama Supabase real de la búsqueda por ubicación y las direcciones quedó terminada y probada. El simulacro local sigue disponible para quien no tenga credenciales.

Notas para el equipo: las credenciales de conexión a la BD quedaron guardadas en `backend/.env` (más `DATABASE_URL` con el pooler de transacciones, para uso interno). Como esas llaves pasaron por varios mensajes del chat del grupo, antes de la entrega conviene rotar la service role key y la contraseña de la base de datos desde el panel de Supabase.

## Pendiente

- Probar el GPS en un celular (en laptop el permiso de ubicación no se puede dar).
- Rotar las llaves de Supabase antes de la defensa, por lo comentado arriba.
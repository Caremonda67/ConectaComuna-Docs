# Orden de despliegue

Salida del 29 de octubre, cuando el login se cayó para todo el mundo durante una hora.

## Qué pasó

Se aplicó `migracion-perfiles-privados-v2.sql` en producción antes de desplegar el frontend que la aprovecha. La migración quitó a `authenticated` el `select` de la tabla `profiles`; el frontend que estaba publicado hacía `select('*')` sobre esa tabla, recibió 403 y no pudo cargar el perfil. Resultado: nadie podía entrar, ni con correo ni con Google, porque la sesión de Supabase sí entraba pero la app moría al leer el perfil.

## La regla

**Una migración que quita permisos va después del despliegue, nunca antes.**

Antes de pegar un `revoke` o un `drop` en producción, la pregunta es una sola: ¿qué consulta hace el frontend que está publicado hoy?

- Si toca lo que voy a cerrar, despliego primero.
- Si no lo toca, da igual el orden.

Al revés, para cambios que **agregan** columnas o tablas: la base primero, y el frontend después.

Son dos reglas opuestas y las dos se rompieron el mismo día:

| Cambio | Orden | Qué pasó cuando se hizo al revés |
|---|---|---|
| Quitar permisos (`revoke select`) | desplegar → migrar | El frontend viejo pedía `select('*')` y el login se cayó para todos |
| Agregar una tabla que el frontend consulta | migrar → desplegar | El frontend nuevo pidió `perfil_contacto` antes de que existiera y el panel de pedidos se quedó en blanco |

La prueba para saber en qué caso estás: busca en el bundle publicado la consulta que va a cambiar. Si el
frontend de hoy la hace y la vas a **cerrar**, despliega primero. Si el frontend de hoy no la hace y la
vas a **abrir**, migra primero.

## Cuando ya no se puede elegir

Si el despliegue se adelantó, el código tiene que aguantar. En pedidos quedó así: antes de consultar,
`orderService` pregunta una vez si la tabla de contacto existe y elige el select. Sin ella el pedido se
ve igual, solo que sin el teléfono de la contraparte. Ese respaldo cuesta una consulta por sesión y
evita que un desfase de cinco minutos tumbe el panel. Cerrar permisos va al revés que agregar.

## Cómo se hace sin adivinar

1. Revisar el bundle publicado antes de tocar la base:
   ```bash
   curl -s https://conecta-comuna-frontend.vercel.app/ | grep -o '/assets/[^"]*\.js'
   ```
   Y buscar en esos archivos la consulta que se va a romper. En este caso, `select('*')` sobre `profiles`.
2. Desplegar el frontend que deja de depender de eso.
3. Aplicar la migración.
4. Probar con una cuenta real: entrar, ver "Mi cuenta", abrir el panel que use esa tabla.

## Plan B, treinta segundos

Si la migración ya está aplicada y algo se cayó:

```sql
grant select on public.profiles to authenticated;
```

Eso devuelve el acceso anterior y desbloquea la app, con el hueco abierto otra vez. Se cierra cuando el frontend nuevo esté publicado, repitiendo el `revoke` y los `grant` por columna.

## Lo que ya protege el código

`profileService.getMine()` intenta `mi_perfil()` y, si esa función no existe todavía, cae a leer la fila propia como antes. Eso cubre el caso "despliego antes de migrar". No cubre el caso contrario, y por eso esta regla existe: cuando la base va primero, el frontend publicado no tiene defensa.

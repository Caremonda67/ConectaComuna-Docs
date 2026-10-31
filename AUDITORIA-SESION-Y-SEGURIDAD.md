# Auditoría de sesión, permisos y despliegue

Fecha: 29 de octubre de 2026. Faltan 15 días para la final.
Alcance: qué puede hacer alguien sin iniciar sesión, qué protege de verdad la base, y en qué estado queda el servicio para publicarlo en Render.
Método: peticiones directas contra la API y contra PostgREST con la clave anónima, más recorrido por el navegador. Todo lo de abajo está comprobado, no deducido del código.

---

## Lo que hay que hacer antes que nada

### La migración de Bogotá: aplicada el 29 de octubre

Durante la primera pasada esta migración todavía no se había corrido y faltaban las columnas del sello. El dueño la aplicó el mismo día y quedó comprobado:

| Qué | Estado |
|---|---|
| `otorgar_sello_territorial` | responde y rechaza anónimos: "Necesitas iniciar sesión para otorgar el sello" |
| `verificar_pedido_publico` | responde |
| `vincular_facilitador_por_codigo` | responde y rechaza anónimos |
| Columnas `verification_by`, `verification_note`, `report_count` | existen |

La verificación pública de pactos funciona de punta a punta: un código inexistente devuelve 404 con mensaje claro ("No se encontró ningún pacto registrado con el código indicado"), donde antes devolvía un 500.

Una nota para quien audite esto después: PostgREST busca las funciones por **nombre de argumento**, no solo por nombre. Llamarlas con `{}` devuelve 404 aunque existan. Hay que mandar los parámetros reales (`{"p_codigo":"..."}`) o el resultado engaña.

### Cualquiera podía descargar los teléfonos de la comunidad

La política de `profiles` es `for select using (true)`, y RLS filtra filas, no columnas. La clave anónima viaja dentro del bundle del frontend, es pública por diseño. Resultado, comprobado contra la base real:

```
curl "<url>/rest/v1/profiles?select=full_name,phone" -H "apikey: <anon>"

[{"full_name":"Kelly Jiménez","phone":"3145558899"},
 {"full_name":"José Rayo","phone":"3172224466"},
 {"full_name":"Wendy Beltrán","phone":"3183334455"}]
```

Nombre y teléfono de cada persona registrada, emprendedores y clientes, en una sola petición y sin cuenta. En un proyecto que cita la Ley 1581 de 2012 en sus propias páginas legales y que tiene 5 de 5 en uso ético de las TIC, es el hallazgo que más daño haría.

Corregido con permisos por columna en `migracion-perfiles-privados.sql`: `anon` se queda con `id`, `full_name` y `avatar_url`, que es lo único que pinta sin sesión (la firma de una reseña). Las pantallas que muestran teléfonos corren siempre con sesión.

**También hay que aplicar esa migración.**

---

## Lo que sí está bien protegido

### La API rechaza a los anónimos, sin excepción

Diez endpoints de escritura y lectura privada, todos responden 401 sin token:

`POST /api/reviews` · `POST /api/reports` · `GET /api/reports` · `PATCH /api/reports/:id` · `POST /api/businesses` · `PATCH /api/businesses/:id` · `POST /api/orders` · `GET /api/orders` · `PATCH /api/orders/:id/status` · `GET /api/auth/mi-cuenta`

### RLS aguanta las escrituras directas

Intenté insertar sin sesión en las cinco tablas que importan: negocios, reportes, reseñas, direcciones y perfiles. Las cinco devolvieron 401.

Un detalle que confunde: `PATCH /businesses` devuelve **204**, que parece éxito. No lo es. Pedí la representación de vuelta y responde `[]`: cero filas afectadas, RLS filtró todo. PostgREST responde 204 aunque no toque nada. Conviene saberlo para no asustarse al auditar.

### Las tablas privadas no se leen sin cuenta

`orders`, `direcciones_usuario` y `reportes_comunitarios` devuelven lista vacía a un anónimo. Correcto.

### Las rutas protegidas redirigen

`/panel`, `/panel/negocio`, `/panel/facilitador`, `/onboarding` y `/verificacion` mandan a `/entrar` sin sesión. Las cinco.

### El asistente distingue bien quién le escribe

| Le pedí sin sesión | Respondió |
|---|---|
| "quiero crear mi negocio" | Reconoce que es visitante anónimo y ofrece "Crear perfil gratis" |
| "mis pedidos activos" | "Para consultar el estado de tus pedidos… ingresa a tu cuenta vecinal" |
| "quiero calificar a un negocio" | "Para calificar… inicia sesión o entra a tu perfil vecinal" |
| "quiero hacer un trato seguro" | Explica el pacto, que no requiere cuenta |

No promete nada que no pueda cumplir y no intenta ejecutar acciones bloqueadas.

### Las páginas públicas no tienen errores

Recorrí portada, explorar, mapa, cómo funciona, trato seguro, términos, privacidad, una ficha de negocio y una ruta inexistente. Cero errores de consola en las nueve.

---

## Lo que se agregó

### Límite de peticiones por IP

El asistente consume cuota de Gemini que se paga, y el servicio va a quedar público. Una pestaña en bucle la agota en minutos.

| Rama | Ventana | Tope |
|---|---|---|
| Asistente (`/api/ai`) | 5 minutos | 20 |
| Escritura (cuentas, reseñas, reportes) | 10 minutos | 40 |
| General (`/api`) | 15 minutos | 300 |

`/api/salud` queda fuera, porque lo consultan el health check de Render y el ping que mantiene el servicio despierto. Comprobado: la petición 21 al asistente devuelve 429 y salud aguanta veinticinco seguidas.

Si el límite corta, el frontend cae a su motor local y el vecino recibe igual una respuesta útil, no un error.

---

## Dos cosas para decidir

### CORS acepta cualquier dominio de Vercel

```js
origenLimpio.includes("vercel.app")
```

Con el backend público, cualquiera que despliegue un sitio en Vercel puede llamar a la API y gastar la cuota de Gemini. El límite por IP amortigua, no cierra. Lo honesto sería poner el dominio exacto del despliegue en `FRONTEND_URL` y quitar el comodín. No lo toqué porque puede romper el despliegue en marcha.

### "Ir a la sección" no dice nada

Cuando el asistente propone navegar, la tarjeta dice "Sección recomendada / Toca el botón para abrir esta sección directamente / Ir a la sección". Para alguien con poca experiencia digital, que es el público del proyecto, eso no informa. Debería decir a dónde va: "Ir a mis pedidos", "Ir a crear mi negocio".

---

## Lo que falta probar

Todo el recorrido **con sesión iniciada**: crear negocio, publicar servicios dictados, pactar un Trato Seguro, calificar, y la bandeja de moderación del facilitador. Hace falta una cuenta de prueba en el Supabase real; las cuentas `@demo.co` solo funcionan en modo demo y la aplicación corre contra la base real.

---

## Segunda tanda: recorrido con sesión iniciada (29 oct)

Hecho en modo demo, levantando la aplicación en el puerto 5174 sin las variables de Supabase. Así corre entera en el navegador con las tres cuentas integradas y nada sale de la máquina. Cubre la interfaz y la lógica de la aplicación, no las políticas de la base, que ya se probaron aparte.

### Lo que funciona

**Dictado con sesión.** Como Doña Marta, dicté "hago arreglos de ropa y cojo bastas, cobro 15 mil". Respondió llamándola por su nombre, reconoció su taller y cambió el botón a "Guardar directamente en mi catálogo" en vez del "Crear negocio" que ve un anónimo. Guardé y el servicio quedó en su catálogo. Volver a tocar el chip no duplica.

**Moderación de reportes, de punta a punta.** Reporté a Cerrajería El Rayo como cliente, el reporte llegó a la bandeja del facilitador con motivo, detalle y quién lo envió, lo descarté con nota de campo, y quedó guardado con estado `descartado`, la nota y el identificador de quien moderó. Pasa a "Ya revisados" y el contador de pendientes baja.

**El modal de reporte está bien resuelto.** Cinco motivos con su explicación, detalle opcional y un aviso de uso responsable que advierte que las denuncias falsas afectan a familias del barrio.

### Lo que estaba roto

**El panel del facilitador se abría con cuenta de cliente.** `/panel/negocio` exigía cuenta de negocio, pero `/panel/facilitador` usaba el guardia sin condición de rol. Entrando como Luisa Martínez (cliente) se veía la bandeja de moderación y los botones de sello y apadrinamiento. Las acciones las frenan RLS y las funciones de la base, pero la pantalla no tenía por qué mostrarse. Corregido con `requireFacilitador`.

**La ficha decía "Rol: Facilitador" escrito a mano**, así que etiquetaba así a cualquiera que entrara. Ahora sale del perfil.

**Al entrar se pasaba por "Completa tu perfil".** `loadUser` publicaba la sesión antes del `await` que trae el perfil, dejando un render con usuario y sin perfil. El guardia lo lee como perfil incompleto y redirige a `/onboarding`. Se veía de refilón en local; con la red de un auditorio, alguien que ya tiene su perfil se queda ahí. Ahora el perfil y el negocio se resuelven antes de publicar la sesión.

**Un reporte se contaba dos veces.** Se guardaba en la base de demostración y además en una clave suelta de `localStorage`, y la bandeja concatenaba las dos listas sin deduplicar. Enviar uno mostraba "2 sin revisar", y el umbral de dos reportes para marcar un negocio bajo observación saltaba con uno solo.

**El panel del negocio no cuadraba.** Mostraba "30.000 generados en 61 servicios", que son 492 pesos por trabajo. La plata salía de sumar los pedidos completados y la etiqueta usaba `completed_orders`, un contador que las semillas dejaron inflado. En la base real pasa igual: Costuras Doña Marta tiene `completed_orders` en 64 con `rating_count` en 4. Las dos cifras salen ahora de los mismos pedidos.

### Detalles menores

- Al enviar un reporte, la confirmación dice que lo revisarán "el equipo de facilitadores y la administración". El rol de administración ya se retiró del proyecto.
- Los contadores de reputación de las semillas (`completed_orders` entre 6 y 64) no se corresponden con los pedidos que existen en la base. Alimentan las insignias públicas tipo "Maestro de la comuna". Decisión de datos, no de código: o se ajustan las semillas o se asume que son históricos previos a la plataforma.

### Lo que sigue sin probarse

El recorrido con sesión **contra Supabase real**. El modo demo ejercita las ramas locales de los servicios, no las consultas ni las políticas de la base. Para eso hace falta una cuenta de prueba en el proyecto real.

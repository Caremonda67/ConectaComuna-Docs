# Auditoría de flujos antes de la final de Bogotá

Fecha: 28 de octubre de 2026. Faltan 16 días para el 13 de noviembre.
Alcance: recorrido de los flujos de ubicación, directorio, pedidos, reseñas, reportes, facilitador y asistente. Revisión contra el código real de `frontend/src` y `backend/src`, no contra la documentación.

Punto de partida: el dueño reportó que al marcar un punto del mapa en Bogotá, la app lo sigue tratando como Cali. Rastreando ese síntoma salieron otros seis del mismo tipo.

Estado de las herramientas: `tsc -b` pasa limpio, `oxlint` no reporta nada. Los fallos de abajo no los ve el compilador, son de lógica y de datos.

---

## 1. La ubicación se fuerza a Cali

### 1.1 El asistente reescribe tu ciudad (bloqueante)

`frontend/src/components/chat/AsistenteComunalModal.tsx:702`

El encabezado del Asistente Comunal concatena "(Cali)" a pelo sobre la etiqueta corta de ubicación. Marques donde marques, el chat dice Cali. Esto es exactamente lo que se vio al probar.

En el mismo archivo, líneas 393 a 407, hay una caja de coordenadas que decide si estás "en zona piloto": latitud entre 3.25 y 3.65, longitud entre -76.7 y -76.35. Fuera de esa caja el asistente te reubica en Nueva Floresta.

El texto incluso nombra la final: dice que detecta el dispositivo fuera de Cali, "por ejemplo, en Bogotá". El problema es que en Bogotá, el día de la final, un jurado que toque el GPS va a leer un mensaje que le explica que lo estamos moviendo de ciudad. Queda raro y abre una pregunta que no queremos.

### 1.2 El GPS arranca creyendo que estás en Cali

`frontend/src/hooks/useGeolocation.ts:20`

El estado inicial de `position` es `COMUNA_CENTER`, el centro de la comuna. Si el permiso se niega o el GPS expira, `position` sigue valiendo Cali y nadie distingue ese valor de una lectura real. El flag `isFallback` existe pero casi nadie lo consulta.

### 1.3 El buscador de barrios solo conoce diez barrios de Cali

`backend/src/routes/ai.js:13` y `frontend/src/hooks/useNeighborhoodLocator.ts:49`

`BARRIOS_COORDENADAS` tiene diez entradas, todas de Cali. `detectarBarrioEnTexto` hace `t.includes(clave)` sobre el mensaje completo, así que tiene dos defectos: no reconoce ningún barrio de otra ciudad, y pisa la ubicación real del usuario si la palabra aparece de refilón en una frase.

En el hook del frontend, cuando Nominatim no devuelve barrio, cae a `BARRIOS_COMUNA[0].nombre`, o sea Antonio Nariño. Estés donde estés.

### Cómo se arregla

Una sola función que resuelva "dónde está el usuario" y la use todo el mundo. Quitar el "(Cali)" pegado, que la etiqueta salga siempre del reverse geocoding. Distinguir tres estados que hoy se confunden en uno: ubicación confirmada, ubicación por defecto, y sin ubicación. Cuando el punto cae fuera del piloto, no reubicar en silencio: mostrar la ciudad real y ofrecer el botón de ver el piloto, que para el jurado se lee mucho mejor.

**Resuelto el 29 de octubre.** La etiqueta del asistente sale del reverse geocoding y ya no lleva "(Cali)" pegado. `useGeolocation` distingue lectura real de valor por defecto con `isFallback`. Si el punto cae fuera del piloto, aparece la ciudad real y un botón explícito para ver los comercios de Cali, sin mover la ubicación. El backend dejó de adivinar barrios por una palabra suelta en el mensaje.

---

## 2. Reportar un negocio no hace nada en producción

`frontend/src/services/businessService.ts:367` escribe el reporte en `localStorage`, y al segundo reporte marca el negocio como `under_review` en la clave `cc_verif_<id>`.

Pero `overlayLocalTrust` (línea 26) hace `if (!isDemoMode) return b` en la primera línea. Y la app corre en modo real: `frontend/.env` tiene las tres variables puestas. Así que en producción esa escritura va a un cajón que nadie abre.

Del lado del servidor, `backend/src/routes/reports.js:31` inserta la fila en `reportes_comunitarios` y ahí se acaba. No toca `report_count` ni `verification_status` del negocio.

Resultado: un vecino reporta, la app dice gracias, y no pasa absolutamente nada. Ni se acumula, ni se marca, ni lo ve un moderador. Peor: la lógica de acumulación que sí existe cuenta reportes por navegador, así que ni siquiera en demo representa a varios vecinos.

### Cómo se arregla

Mover el umbral al backend. Después del insert, contar los reportes pendientes del negocio y actualizar `report_count` y `verification_status` en la misma operación. Borrar las escrituras a `localStorage` del camino real.

**Resuelto el 29 de octubre.** El reporte entra directo a `reportes_comunitarios` por PostgREST. El conteo y el estado del negocio los recalcula el trigger `refresh_business_reports` cada vez que el equipo confirma o descarta un caso. Ya no hay escrituras a `localStorage` en el camino real. El umbral cambió de dos reportes pendientes a dos reportes confirmados: así una campaña de reportes no esconde a nadie.

---

## 3. Hay un endpoint de moderación que nadie llama

`PATCH /api/reports/:id` está escrito, valida rol facilitador y guarda `moderado_por_id`. Ninguna pantalla lo invoca. Busqué en todo `frontend/src` y no aparece.

Lo mismo con `GET /api/reports`. Existe, funciona, no tiene consumidor.

O sea: la moderación está a medias. La mitad del servidor está lista y la interfaz nunca se hizo.

**Resuelto a medias el 29 de octubre.** La pantalla existe: `ReportesComunitariosPanel`, con modo equipo y modo acompañante. Pero sigue usando PostgREST, no estos endpoints. `GET /api/reports` y `PATCH /api/reports/:id` continúan sin consumidor y quedan en el inventario de la sección 9.1.

---

## 4. El rol Administrador está documentado y no existe

`AGENTS.md` describía cuatro roles. El enum de la base tiene tres: `client`, `business`, `facilitador`.

No hay ruta `/panel/admin` en `router.tsx`, ni pantalla, ni guard. El único rastro es un tipo suelto en `frontend/src/types/ai.ts:86` que incluye `'admin'` y no se usa en ninguna parte.

Si un jurado abre el documento y pregunta por el administrador, no hay qué mostrar. Hay que decidir: implementarlo o sacarlo de la documentación. Con 16 días, sacarlo de la documentación y dejar la moderación en manos del equipo de Conecta Comuna es más honesto y defendible.

**Resuelto (28 oct):** el rol salió de la documentación y de `AGENTS.md`. La moderación queda en la mesa del equipo (`/panel/equipo`) y el facilitador solo ve y responde por los negocios que apadrina. El tipo suelto de `ai.ts` se limpió.

---

## 5. La tabla `direcciones_usuario` no está en ningún schema

`direccionService.ts` lee y escribe `direcciones_usuario`. Esa tabla no aparece en `backend/schema.sql`, ni en `frontend/supabase/schema.sql`, ni en `aplicar-completo.sql`. Confirmado con grep sobre los tres archivos.

Según `EVIDENCIA-Rama-Real-Supabase.md` la tabla se creó y se probó contra Supabase. El problema es que se creó a mano en la consola y nunca se escribió en el archivo. Hoy el schema del repositorio no reconstruye la base.

Encima hay tres copias del schema y ya se separaron entre ellas. El `diff` entre backend y frontend muestra diferencias reales, no solo comentarios: el `create type order_status` está en lugares distintos y `refresh_business_rating` usa `or replace` en una copia y no en la otra.

### Cómo se arregla

Un solo schema como fuente de verdad, con la tabla que falta escrita adentro. Las otras copias se borran y el README apunta a la única ruta válida.

**Resuelto a medias el 29 de octubre.** `direcciones_usuario` ya está escrita en la migración y en las tres copias, con RLS por usuario. Las tres copias siguen existiendo, pero comparten la misma cola. Falta decidir cuál queda como fuente única después de la final.

---

## 6. El buscador de direcciones se cae si Nominatim lo bloquea

`frontend/src/services/direccionService.ts:198` captura el error de red y cae a filtrar `LUGARES_DEMO`, que son seis puntos de Cali. Si escribes "Chapinero" y Nominatim no responde, la app dice "Sin coincidencias" como si el barrio no existiera.

Y es probable que no responda. El `useEffect` de `UbicacionPicker.tsx:56` dispara la búsqueda con 250 ms de debounce. Nominatim pide máximo una consulta por segundo. Escribiendo normal se superan tres o cuatro por segundo, y la respuesta es 429.

Además no hay `AbortController`: dos respuestas que vuelven en desorden dejan en pantalla las sugerencias de lo que se escribió antes.

### Cómo se arregla

Subir el debounce a 600 ms o más, abortar la petición anterior, y cambiar el mensaje de error para que diga que el buscador no está disponible en vez de mentir diciendo que no hay resultados.

---

## 7. Cosas menores que conviene cerrar

- `backend/src/routes/ai.js` tiene 1298 líneas, el 63% de todo el backend en un archivo. Choca con la regla de `AGENTS.md` sobre rutas monolíticas. Un jurado técnico que abra el repositorio lo va a ver.
- Cero tests en los dos repositorios. La rúbrica castiga en "Dominio conceptual y programación" (9.0 de 10).
- El trigger `refresh_business_rating` solo corre `after insert`. Si algún día se edita o borra una reseña, el promedio queda viejo. Hoy no hay endpoint que lo haga, así que no es urgente.
- Nombres de tablas mezclados: `businesses`, `orders`, `reviews` en inglés conviven con `facilitadores_negocio`, `reportes_comunitarios` y `direcciones_usuario` en español. `REGLA-IDIOMA-BD` pide español para lo nuevo, y así quedó, pero la mezcla se nota. A estas alturas renombrar es más riesgo que beneficio: mejor tener lista la explicación.

---

## Plan de corrección

Ordenado por lo que más daño hace en tarima.

### Bloque 1, los tres primeros días

1. Quitar el "(Cali)" pegado del asistente y hacer que la etiqueta venga del reverse geocoding.
2. Centralizar la resolución de ubicación en un solo módulo con los tres estados separados.
3. Cambiar el desvío silencioso a Nueva Floresta por un aviso que muestre la ciudad real y ofrezca entrar al piloto.

Con esto el bug reportado queda cerrado y el recorrido en Bogotá deja de delatarse.

### Bloque 2, cuatro días

4. Mover el umbral de reportes al backend y borrar el `localStorage` del camino real.
5. Pantalla de moderación en el panel del facilitador, enganchada a `GET` y `PATCH /api/reports` que ya existen. Es la de mejor relación esfuerzo-resultado: el servidor ya está hecho.
6. ~~Decidir qué pasa con el rol Administrador~~ · hecho el 28 oct.

### Bloque 3, tres días

7. Escribir `direcciones_usuario` en el schema, dejar un solo archivo y verificar que levanta una base desde cero.
8. Arreglar el debounce, el abort y el mensaje del buscador de direcciones.

### Bloque 4, lo que quede

9. Partir `ai.js` en módulos por responsabilidad.
10. Tests sobre lo que más se va a demostrar: distancias, umbral de reportes, resolución de ubicación y verificación del Trato Seguro.

### Antes de viajar

Recorrer la app completa con el GPS puesto en Bogotá y con el GPS negado, que son los dos escenarios reales del 13 de noviembre.


---

## 8. Lo que se cerró el 28 de octubre (segunda pasada)

Recorrido completo contra el código, después de la primera auditoría. Estos cambios están en el árbol de trabajo, sin commitear.

### 8.1 El sello territorial lo escribe una función de la base

`verificarTerritorialmente` hacía un `update` sobre `businesses` y el trigger `businesses_protect_columns` lo rechazaba. Como el error no se miraba, la interfaz decía "listo" y la base quedaba igual. Ahora hay dos funciones `security definer`, `otorgar_sello_territorial` y `revocar_sello_territorial`, que comprueban que quien firma tenga vinculación aprobada y escriben estado, nombre, fecha y nota. La pantalla del facilitador muestra el error cuando la función lo devuelve.

### 8.2 El QR del Trato Seguro ya no depende de Render

La verificación pública se resolvía llamando a `GET /api/orders/verificar/:codigo`. En el plan gratuito de Render el servicio se duerme y el primer escaneo podía tardar un minuto. Ahora la consulta la resuelve `verificar_pedido_publico(codigo)` en Postgres, con acceso para `anon` y sin abrir la tabla `orders`. La API queda como respaldo para bases donde la función no esté aplicada.

De paso el endpoint tenía dos fallas: traía los últimos 100 pedidos y comparaba prefijos en memoria (cualquier QR viejo devolvía "no existe") y aceptaba códigos de un carácter, lo que permitía barrer pedidos ajenos. Ahora exige ocho caracteres y resuelve por la misma función.

### 8.3 Reportes: el umbral vive en la base

`refresh_business_reports` cuenta los reportes pendientes después de cada insert, actualización o borrado, escribe `report_count` y pone el negocio `under_review` al llegar a dos. También hay un `update` de arranque que deja el conteo al día para los negocios que ya tenían reportes. Antes esa regla solo existía en modo demo y contaba por navegador.

### 8.4 Vincular un facilitador por código estaba roto

`vincularConCodigo` insertaba la fila con `estado_vinculacion = 'aprobado'`, pero la política `facilitadores_insert_solicitud` solo permite insertar en `pendiente`. Contra la base real eso devuelve error de RLS. Ahora hay una función `vincular_facilitador_por_codigo(codigo)`: busca el negocio por su código de apadrinamiento, revisa si ya hay vinculación y la deja aprobada. El código funciona como autorización del dueño, que es lo que es.

### 8.5 Los avisos de pedido nuevo nunca llegaban en un proyecto nuevo

`useRealtimeOrders` escucha `postgres_changes` sobre `orders`, pero ningún schema agregaba la tabla a la publicación `supabase_realtime`. En un proyecto recién creado el canal existe y no emite nada. La migración la agrega si falta.

### 8.6 El asistente tiene tope

`POST /api/ai/chat` era público, sin límite y sin control de tamaño: una pestaña olvidada podía gastar la cuota de Gemini del mes. Ahora hay 30 mensajes por IP cada 10 minutos, tope de 800 caracteres por mensaje, historial recortado a 10 turnos de 600 caracteres y cuerpo máximo de 128 kB. Se activó `trust proxy` en Express para que la IP sea la del visitante y no la del balanceador de Render. La llamada del navegador se corta a los 20 segundos y cae al motor local, así el chat no se congela si el backend está arrancando.

### 8.7 El frontend dejó de llamar a Express para lo que ya resuelve Postgres

Crear pedido y crear reporte pasaban primero por la API. Con Render dormido eso significaba esperar hasta un minuto para una operación que Supabase hace directo y con las mismas reglas. Ahora el navegador escribe en Postgres y Express queda solo para el asistente. El arranque de la app hace una llamada a `/api/salud` en segundo plano para despertarlo antes de que alguien abra el chat.

### 8.8 Detalles que salieron en la pasada

- La ruta `verificar-trato` era un alias de `trato-seguro/verificar`. Se quitaron las dos entradas duplicadas del router.
- `cambiarEstadoObservacionNegocio` no se llamaba desde ninguna parte. Se borró.
- `POST /api/orders` devolvía la fila sin negocio ni cliente; el pedido recién creado aparecía como "Taller Comunal" hasta recargar. Ahora devuelve las relaciones.
- El equipo pasó de 4 a 3 integrantes en la documentación. Sebastián queda como la persona de frontend.

### 8.9 La clave de Gemini está sin cupo

Probando el endpoint contra el servidor local salió el problema: `gemini-3.6-flash` responde 429 con `generate_content_free_tier_requests, limit: 20`. El plan gratuito da 20 peticiones diarias por modelo, y el asistente gasta una en cualquier pregunta abierta. Para la final toca poner una clave con facturación, o al menos dosificar los ensayos y no gastarla en pruebas de escritorio.

De paso quedaron dos ajustes para no quemar cupo: un saludo suelto ("hola", "buenas tardes") lo contesta el motor local, y un modelo que devuelve 429 queda diez minutos fuera de la cascada, así el mensaje siguiente no pierde dos llamadas antes de llegar al que sí responde.

---

## 9. Código que hoy no sirve

Inventario de lo que está escrito y nadie ejecuta. No todo hay que borrarlo antes del 13; conviene saberlo para no defenderlo como si estuviera en uso.

### 9.1 Rutas del backend sin consumidor

| Ruta | Líneas | Estado |
|------|--------|--------|
| `backend/src/routes/auth.js` | 66 | El registro va directo a Supabase Auth. |
| `backend/src/routes/businesses.js` | 151 | El frontend lee y escribe `businesses` por PostgREST. |
| `backend/src/routes/reviews.js` | 51 | Las reseñas se insertan por PostgREST. |
| `backend/src/routes/reports.js` | 73 | La moderación vive en `ReportesComunitariosPanel` y usa PostgREST. |

Son 341 líneas de API que funcionan pero no tienen quién las llame. Mantenerlas cuesta poco; el riesgo es que un jurado pregunte por un endpoint y la respuesta sea "eso no lo usa la app".

### 9.2 Duplicación del motor del asistente

`frontend/src/services/aiService.ts` (832 líneas) y `backend/src/routes/ai.js` (1239 líneas) tienen dos motores de respaldo con la misma lógica: intenciones, códigos, pedidos, saludos. El del backend además concentra el 63% del backend en un archivo, contra la regla de rutas cortas de `AGENTS.md`.

### 9.3 Restos pequeños

- `components/ui/Badges.tsx` exporta `BadgePill` y nadie lo usa.
- `lib/authRedirect.ts` mantiene `peekAuthFrom`, que no se llama.
- `types/index.ts` conserva `Producto` y `CategoryStyle`, de una versión anterior del catálogo.
- `services/demoBackend.ts` exporta `writeDb` y `resetDb` sin consumidores.
- `direccionService.ts` conserva `LUGARES_DEMO`, que solo se usa como respaldo del buscador.
- `orderService.ts` guarda tres comprobantes de muestra (`CC-DEMO2026`, `CC-ORD-1`, `CC-ORD-2`), marcados con `ejemplo: true` y sin nombres ni direcciones reales. Los dos que apuntaban a pedidos vivos (`CC-93E416F2` y `CC-E9894D7D`) se quitaron. Primero se consulta la base; el ejemplo solo aparece si esa consulta no encuentra nada.

### 9.4 Lo que no se tocó y por qué

Las tres copias del schema siguen existiendo (`frontend/supabase/schema.sql`, `aplicar-completo.sql`, `backend/schema.sql`). Ahora las tres llevan la misma migración al final, pero siguen siendo tres. Unificarlas implica decidir cuál es la buena y borrar las otras; es trabajo de media hora que conviene hacer después de la final, no antes.

---

## 10. Para dejar la demo con el backend dentro

1. ~~Correr `frontend/supabase/migracion-bogota.sql` completo en el editor SQL de Supabase.~~ Hecho el 29 de octubre. La migración está aplicada en la base viva y se corrió tres veces seguidas sin error.
2. Redesplegar el backend en Render. El servicio ya existe en `https://conectacomuna-backend-1fl5.onrender.com`, pero sigue sirviendo el código anterior. Cuando el PR entre a `main`, hay que lanzar el deploy con las variables `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `GEMINI_API_KEY` y `FRONTEND_URL`.
3. En Vercel, proyecto del frontend, agregar `VITE_API_URL=https://conectacomuna-backend-1fl5.onrender.com` y volver a desplegar. Sin esa variable la app usa el motor local del asistente, que funciona, pero no llama a Gemini. Sigue pendiente.
4. Probar en el celular: abrir la app, esperar la primera carga, abrir el asistente y mandar una pregunta abierta ("busco quién arregle una silla coja"). Si responde distinto al motor de reglas, el backend está enganchado. La primera carga despierta Render; el plan gratuito duerme el servicio a los 15 minutos sin visitas.
5. Escanear un QR de Trato Seguro real. Debe abrir sin sesión y sin esperar el arranque de Render. Probado contra la base con `93e416f2`: responde sin cliente ni dirección.
6. Anotar en el guion qué se muestra si falla la red del evento: el asistente tiene motor local y el QR lo resuelve Postgres, así que la demo aguanta sin internet en Render. Lo único que no se puede caer es el wifi para llegar a Supabase.

---

## 11. Revisión de seguridad y de sentido (29 de octubre)

Antes de commitear nada hice dos pasadas: una sobre el código que corre, otra sobre lo que la aplicación promete. La segunda salió peor que la primera.

### 11.1 El chat afirma que encontró el código antes de buscarlo

El bloque de códigos se ejecuta antes de mirar si hay sesión y antes de la consulta de pedidos. Está igual en los dos motores, `frontend/src/services/aiService.ts:235` y `backend/src/routes/ai.js:270`:

```js
let detalle = `¡He ubicado el acuerdo ${cod} en el registro comunal!`
```

Nadie consultó la base. Si el código coincide con uno de los pedidos activos del usuario, agrega trabajo, contraparte, estado y valor. Si no coincide, la frase queda igual y solo aparece un botón que lleva a la página pública.

Los cuatro casos, con el comportamiento de hoy:

| Lo que escribe | Qué pasa |
|---|---|
| Un código suyo, activo | Datos reales, pero te saca de la conversación |
| Un código suyo, completado o cancelado | "Lo ubiqué" sin datos, porque la lista solo trae `pending`, `accepted` e `in_progress` (`AsistenteComunalModal.tsx:164`) |
| Un código ajeno | "Lo ubiqué" y te lleva a ver el nombre y la dirección de otra persona |
| Un código que no existe | "Lo ubiqué" y la página siguiente dice que no existe |

El último caso es el que rompe la demo. Un jurado escribe `CC-1234`, el asistente dice que lo encontró, y la pantalla siguiente lo desmiente.

Lo que debería hacer, con una sola consulta a la base:

1. **Es suyo.** Tarjeta del acuerdo dentro del chat, con datos reales y botones para ver el comprobante o abrir una disputa. Sin navegar.
2. **Existe y no es suyo.** "Ese código existe, pero no está asociado a tu cuenta." Nada de nombre ni dirección.
3. **No existe.** "No encontré ningún acuerdo con ese código." Y ofrecer crear uno.

La tarjeta ya existe: `AsistenteComunalModal.tsx:966`, tipo `acuerdo_trato_seguro`. Solo hay que alimentarla con la respuesta real en vez de mandar al usuario a otra página.

**Resuelto el 29 de octubre.** `responderConsultaDeCodigo` consulta `verificar_pedido_publico` antes de abrir la boca. Si el código es de la persona, responde con la tarjeta y el botón "Ver comprobante" abre el recibo dentro del chat. Si existe y es ajeno, dice que existe con negocio y monto, sin nombre ni dirección. Si no existe, lo dice con esas palabras. Los dos motores dejaron de inventar contenido de códigos.

### 11.2 Quién puede ver un comprobante

Hoy, cualquiera. `verificar_pedido_publico` es `security definer`, tiene `grant execute` a `anon` y devuelve nombre completo del cliente, dirección de entrega, montos y el UUID del pedido. La página lo pinta tal cual (`TratoSeguroVerificationPage.tsx:365` y `:377`).

| Quién | Hoy ve | Debería ver |
|---|---|---|
| Cliente del pedido | Todo | Todo |
| Dueño del negocio | Todo | Todo |
| Facilitador vinculado | Todo | Todo |
| Usuario con sesión, ajeno | Todo | Que el código existe, el negocio, el estado y el monto |
| Cualquiera sin sesión | Todo | Lo mismo que el ajeno |

**Resuelto el 29 de octubre.** La función devuelve tres niveles. `participante` trae todo; `publico` trae código, negocio, estado, montos y modalidad, sin `client`, sin `delivery_address` y sin el UUID del pedido. El nivel lo decide la base con `auth.uid()`, no el navegador. Probado por REST sin sesión con el pedido `93e416f2`: la respuesta no incluye cliente ni dirección.

El QR está pensado para que el cliente lo muestre, así que la página pública tiene que seguir abriendo sin sesión. Lo que no tiene por qué abrir es la dirección de su casa.

La página pública muestra dos códigos de ejemplo (`CC-DEMO2026` y `CC-ORD-1`) con un aviso de "ejemplo" antes de abrirlos.

### 11.3 Huecos de acceso

Ordenados por lo que pueden romper.

**1. Cualquiera se convierte en facilitador (P0).** `profiles_update_own` no limita columnas. Un `update profiles set account_type = 'facilitador'` y esa cuenta entra a `reportes_select_facilitador`: ve todos los reportes de la comuna y puede moderarlos. Desde ahí, con un código de apadrinamiento, se vincula a un negocio y se otorga el sello.

**2. El código de apadrinamiento se puede adivinar (P0).** Son seis dígitos generados con `Math.random()` (`facilitadorService.ts:146`), o sea 900.000 combinaciones. No vence, no se rota y `vincular_facilitador_por_codigo` no cuenta intentos. Un script lo recorre en minutos.

**3. Las políticas de `orders` dejan escribir cualquier columna (P0).** `orders_update_involved` no tiene `with check`, así que cliente y dueño pueden cambiar `status`, `final_price`, `advance_payment`, `delivery_address` y `business_id`. La máquina de estados solo existe en `ORDER_TRANSITIONS` (`lib/utils.ts`). Con la clave anónima, un cliente marca `completed` un pedido que nunca recibió y deja reseña. El tope del 50% se esquiva cambiando precio y anticipo en el mismo update, porque el `check` se evalúa sobre la fila nueva.

**4. Borrar el negocio borra los comprobantes del cliente (P0).** `orders.business_id` es `on delete cascade` y `businesses_owner_write` es `for all`. El emprendedor elimina su negocio y con él desaparecen los pedidos. Justo lo que el comprobante promete conservar.

**5. Las fotos de los pedidos son públicas (P0).** El bucket `order-photos` es público y `order_photos_read` solo filtra por `bucket_id`. `anon` puede listar y abrir las fotos, que suelen incluir la fachada o el interior de una casa.

**6. `profiles` está abierta a cualquier sesión (P1).** `profiles_select_public using (true)` y `grant select` completo a `authenticated`. Cualquier usuario descarga nombre, teléfono y barrio de todo el mundo. A `anon` se le revocaron columnas, a `authenticated` no.

**7. `bump_completed_orders` cuenta de más (P1).** Suma uno cada vez que el pedido pasa a `completed` desde otro estado. Como la base permite volver atrás, alternar estados infla el contador.

**8. Reportes sin límite (P2).** `reportes_insert_auth` solo exige que el autor sea quien inserta. Dos reportes de la misma persona dejan el negocio en `under_review`.

Aparte: la función pública devuelve el UUID completo del pedido a cualquiera. Ese UUID es la llave que usa el respaldo del frontend para leer la tabla.

**Estado al 29 de octubre:**

1. Cerrado. El trigger `profiles_proteger_tipo` bloquea el cambio a `facilitador` desde una cuenta normal, y el registro y el onboarding ya no ofrecen ese tipo. Las cuentas de facilitador las asigna el equipo.
2. Cerrado. El código tiene 8 caracteres alfanuméricos, vence en 24 horas, se gasta al usarlo y lo genera una función de la base. Un código con expiración `NULL` se rechaza. Sigue sin contar intentos, pero el espacio de 36^8 hace que barrerlo no sea barato.
3. Cerrado. Se eliminó la política de `update` directo. `cambiar_estado_pedido` valida transición, rol, tope del 50% y que el pedido no esté cerrado.
4. Cerrado. `orders.business_id` quedó `on delete restrict`.
5. A medias. El bucket `order-photos` sigue público para no romper las URLs guardadas, pero se cerró el listado anónimo y la lectura por carpeta ajena. Quien tenga el enlace exacto de una foto todavía puede abrirla. Pasarlo a privado implica firmar URLs y tocar los pedidos viejos; queda para después de la final.
6. Abierto. `profiles` sigue legible por cualquier sesión. El panel ya no pide el nombre ni el teléfono de quien reporta al negocio, pero con devtools y un UUID todavía se puede consultar. Es P1.
7. Cerrado. `bump_completed_orders` solo suma la primera vez que el pedido llega a `completed`.
8. Cerrado. Un reporte por negocio y por persona cada 30 días. El negocio queda bajo observación a los dos reportes confirmados, no a los dos pendientes.

El UUID ya no sale en la respuesta pública.

### 11.4 Los huecos de producto

Aquí está lo que de verdad puede acorralarlos, porque no se arregla con una política.

**El dinero nunca pasa por la plataforma.** No hay recaudo, retención ni devolución. El "tope del 50%" es una restricción sobre un campo que llena el emprendedor al aceptar. Si el cliente gira el 100% por Nequi, la aplicación no se entera. Decir "protegemos tu anticipo" es falso y se cae con una pregunta.

**La JAC no existe como actor.** La cláusula de mediación era una cadena de texto y el sello imprimible decía "COMISIÓN DE CONVIVENCIA JAC", con el recibo hablando de "Garantía Vecinal de la JAC". Nadie de la JAC había firmado nada, nadie recibía los casos y no había acta. El 29 de octubre se cambió el texto: el sello dice "verificación en territorio", la cláusula habla del equipo de Conecta Comuna y se quitó la cita a la Ley 2166. Si un jurado pregunta, la respuesta es que la JAC no respalda el sello porque no hay acta que lo soporte.

**El pedido se cierra de un solo lado.** El emprendedor marca `completed`, sube su contador y habilita la reseña. El cliente no confirma la entrega. Entonces la reputación mide lo que el vendedor dice de sí mismo.

**La página de inicio ya no promete de más.** El texto pasó a "Cuando un trabajo se pacta por ConectaComuna puedes dejar un comprobante de Trato Seguro". El botón principal de la ficha sigue llevando a WhatsApp y ese camino no crea pedido, así que el Trato Seguro es una opción, no la regla.

**El Trato Seguro se puede saltar.** `shouldSkipTratoSeguro()` guarda una bandera en `sessionStorage` y el aviso deja de salir. El camino corto es el que la gente usa.

**No hay disputa.** Si el emprendedor cobra el anticipo y no entrega, no hay botón para reclamar contra ese pedido. `reportes_comunitarios` denuncia negocios por categoría, no pedidos, y lo ven el equipo y el facilitador del negocio. La protección termina justo donde empieza el problema.

**El sello ahora no promete respaldo externo.** Lo otorga un facilitador vinculado, no una autoridad. La tabla guarda `verification_by` y `verification_date`, pero la interfaz todavía no muestra quién ni cuándo.

### 11.5 Cómo debería funcionar, en el papel

Si separamos lo que la aplicación puede hacer de lo que no, el discurso se sostiene.

Puede: dejar constancia de lo acordado con un código que cualquiera comprueba, impedir que se registre un anticipo mayor al 50%, congelar el acuerdo para que nadie lo edite después, darle al equipo de Conecta Comuna una cola de casos con evidencia y construir reputación que no se infle sola.

No puede: mover dinero, obligar a devolverlo, verificar identidad ni garantizar la entrega. Decirlo en la landing cuesta menos que perder el punto en la ronda de preguntas.

Flujo propuesto:

1. El cliente entra a la ficha y toca "Pedir con Trato Seguro". WhatsApp queda como "preguntar antes", no como el botón grande.
2. Llena qué necesita, para cuándo y dónde. Sin precio cerrado.
3. El emprendedor acepta y ahí fija precio y anticipo. El acuerdo se congela: precio, anticipo, fecha y dirección ya no se editan.
4. Salen dos comprobantes con el mismo código. Uno para cada parte.
5. El emprendedor marca "entregado". El cliente confirma "recibido". Solo entonces el pedido queda `completed`.
6. Si el cliente no confirma en siete días, el pedido queda `delivered` sin sumar reputación. No se cierra solo a favor de nadie.
7. Si alguien abre una disputa, el pedido pasa a `disputed`, se congela y aparece en la cola del equipo con todo el historial.
8. La reseña solo se puede dejar después de `completed` confirmado por el cliente.

Estados y quién los mueve:

| Estado | Quién lo pone | Qué significa |
|---|---|---|
| `pending` | Cliente | Solicitó el servicio |
| `accepted` | Emprendedor | Aceptó y fijó precio y anticipo |
| `in_progress` | Emprendedor | Empezó el trabajo |
| `delivered` | Emprendedor | Dice que entregó, falta el cliente |
| `completed` | Cliente | Confirma que recibió |
| `cancelled` | Cualquiera, con motivo | Se cayó el trato |
| `disputed` | Cualquiera | Hay un reclamo abierto |

Eso cambia tres cosas de la base y una del discurso. La base: una función `cambiar_estado_pedido` que valide transición y rol, un estado `delivered` y una tabla de disputas. El discurso: "dejamos constancia y el equipo media", en vez de "te protegemos la plata".

### 11.6 Qué tocar antes del 13

**Estado al 29 de octubre:** los nueve P0 quedaron implementados. El punto 6 quedó a medias: el bucket sigue público para no romper URLs, pero se cerró el listado anónimo. El detalle está en la sección 12.

**P0, sin esto no se presenta:**

| # | Qué | Dónde | Costo |
|---|---|---|---|
| 1 | Correr `migracion-bogota.sql` | Supabase | 10 min |
| 2 | Verificación pública por niveles (participante / ajeno / anónimo) | función SQL | 1 h |
| 3 | Chat que verifica de verdad y muestra la tarjeta en línea | `aiService.ts`, `ai.js`, modal | 4 h |
| 4 | Bloquear el autoascenso a facilitador | `profiles_update_own` + trigger | 1 h |
| 5 | Estados de pedido por RPC con reglas | migración + `orderService.updateStatus` | 3 h |
| 6 | Bucket `order-photos` privado | migración | 20 min |
| 7 | `on delete restrict` en `orders.business_id` | migración | 10 min |
| 8 | Código de apadrinamiento de 8 caracteres, con vencimiento | `facilitadorService`, RPC | 2 h |
| 9 | Corregir la frase de la landing y el texto del sello | `HomePage`, `SelloOficialComunal` | 30 min |

**P1, si alcanza:**

| # | Qué | Costo |
|---|---|---|
| 10 | Estado `delivered` y confirmación del cliente | 1 día |
| 11 | Disputa con cola para el equipo | 1 día |
| 12 | `bump_completed_orders` que cuente una sola vez (hecho) | 30 min |
| 13 | Teléfono de `profiles` solo para quien lo necesita | 1 h |
| 14 | Un solo motor del asistente | 1 día |

**P2, después de la final:** buscador de direcciones con debounce y aborto, unificar los tres schemas, borrar las rutas sin consumidor y pasar `order-photos` a privado con URLs firmadas.

### 11.7 Lo que se dice en la ronda de preguntas

Con el flujo de arriba, estas respuestas se sostienen:

- "No movemos dinero. Dejamos constancia de lo acordado con un código que cualquiera comprueba. Si hay un reclamo, el equipo de Conecta Comuna revisa el caso."
- "El anticipo no puede quedar registrado por encima del 50%. Si alguien paga por fuera, no tenemos forma de saberlo y lo decimos."
- "El negocio marca el pedido como completado y ahí se habilita la reseña. La confirmación del cliente y la disputa están diseñadas, pero no entraron en esta versión."
- "La dirección no es pública. Quien escanea el QR ve que el acuerdo existe, con qué negocio y por cuánto. Los datos de la casa solo los ven las dos partes."
- "El sello lo otorga un facilitador vinculado y queda con nombre y fecha. No decimos que lo respalde la JAC."
- "El QR no depende de que Render esté despierto: lo resuelve la base de datos."

Lo último ya se resolvió: el texto del sello y la cláusula se cambiaron el 29 de octubre. Si algún día hay acta de la JAC, se puede volver a nombrar.

---

## 12. Lo que quedó listo el 29 de octubre

La migración se aplicó en la base viva (`xzsqdyluhawtpoxbldak`) y se corrió tres veces seguidas sin error. Las tres copias del schema (`frontend/supabase/schema.sql`, `aplicar-completo.sql` y `backend/schema.sql`) llevan la misma cola que `migracion-bogota.sql`.

### 12.1 Migración

- `equipo_conecta`: lista corta del equipo que media. No es un rol del producto y no se puede confundir con facilitador.
- Verificación por niveles: `participante` ve todo; `publico` ve código, negocio, estado, montos y modalidad, sin cliente, dirección ni UUID del pedido.
- `cambiar_estado_pedido`: valida transición, rol, tope del 50% y motivo de cancelación. Se eliminó la política que permitía escribir `orders` a mano.
- `profiles_proteger_tipo`: bloquea el autoascenso a facilitador. El registro y el onboarding ya no ofrecen esa opción.
- Reportes: el equipo confirma o descarta, el facilitador solo ve los negocios que apadrina y ambos pueden responder. Dos reportes confirmados dejan el negocio bajo observación.
- `sellar_reporte`: la base anota quién decidió y cuándo.
- Código de apadrinamiento: 8 caracteres alfanuméricos, vence en 24 horas, se gasta al usarlo y lo genera una función. Los códigos viejos con expiración `NULL` se rechazan.
- `orders.business_id` quedó `on delete restrict`; el bucket `order-photos` ya no se puede listar sin sesión.

### 12.2 Pruebas que se corrieron

| Prueba | Resultado |
|---|---|
| Migración repetida tres veces | Sin error, objetos ya existentes |
| `sellar_reporte` al cambiar estado | `moderado_por_id` queda con la cuenta que decidió |
| `report_count` después de confirmar y descartar | Se recalcula con los confirmados, no con los pendientes |
| Código vencido | Bloqueado |
| Código con expiración `NULL` | Bloqueado |
| Código vigente | Vincula y se gasta |
| `verificar_pedido_publico('93e416f2')` sin sesión | 200, `nivel=publico`, sin cliente ni dirección |
| Código de 6 caracteres | 200 con `null`, no consulta |
| `es_equipo_conecta` sin sesión | 401, permiso denegado |
| `cambiar_estado_pedido` sin sesión | 401, permiso denegado |
| Backend local `/api/salud` | 200 |
| Backend local `CC-93E416F2` | 200 |
| Backend local `CC-93E416` | 400, código incompleto |
| Backend local `abc` | 400, código inválido |
| Backend local `CC-DEMO2026` | 200, ejemplo |
| `tsc -b` | 0 errores |
| `oxlint src` | 0 hallazgos |
| `vite build` | Compila |
| `node --check` en todo `backend/src` | Sin errores de sintaxis |
| `npm audit` en los dos repos | 0 vulnerabilidades |

### 12.3 Lo que falta antes de viajar

1. Mergear los PR y redesplegar Render. Hoy el servicio sigue con el código anterior.
2. Definir `VITE_API_URL=https://conectacomuna-backend-1fl5.onrender.com` en Vercel y redeployar el frontend.
3. Insertar los tres UUID en `equipo_conecta`. El `insert` sigue comentado en la migración y, sin esas filas, nadie puede confirmar ni descartar reportes en producción.
4. Recorrer la app en el celular con el GPS en Bogotá y con el GPS negado.
5. Escanear un QR real desde un teléfono sin sesión y confirmar que no muestra nombre ni dirección.

### 12.4 Pendientes conscientes

- La confirmación del cliente y la disputa (P1) no se implementaron. No se presentan como si existieran.
- `profiles.phone` sigue legible por cualquier cuenta autenticada. El panel ya no lo pide al negocio reportado, pero con devtools y un UUID se puede consultar.
- El bucket `order-photos` sigue público. Se cerró el listado, no el acceso por URL exacta.
- El buscador de direcciones conserva el debounce corto y no aborta la petición anterior.
- `backend/src/routes/ai.js` sigue siendo un archivo grande y no hay tests automatizados en ninguno de los dos repos.


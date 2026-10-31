# Plan de corrección hasta la final

Del jueves 29 de octubre al viernes 13 de noviembre de 2026. Quedan 15 días, de los cuales uno es festivo (2 de noviembre) y dos son de viaje y ensayo.

Este plan sale del análisis completo de los dos repositorios hecho el 29 de octubre: 247 archivos, 43.089 líneas, revisión de git, esquema, documentación y una corrida de `tsc -b` y `oxlint`.

## Criterio de éxito

El 13 de noviembre, en un portátil ajeno y con el wifi del evento, la app hace esto sin mentir y sin caerse:

1. Abre el directorio y el mapa con negocios reales.
2. El asistente responde algo útil y no inventa negocios, teléfonos ni códigos.
3. Se crea un pedido, el negocio lo acepta con anticipo del 50% y el cliente ve el comprobante por QR.
4. Un facilitador vinculado otorga el sello y el sello aparece en la ficha.
5. Quien pregunta cómo se modera un reporte recibe la respuesta correcta: lo hace el equipo.

Todo lo demás de este documento existe para que esos cinco puntos aguanten.

---

## Bloque 0. Desplegar lo que ya está hecho

Hoy `main` está 8 commits atrás en el backend y 28 en el frontend. Render y Vercel despliegan `main`, así que la app publicada no tiene los arreglos del 29 de octubre, entre ellos el cierre de la lectura anónima de teléfonos.

| ID | Tarea | Dónde | Cómo se arregla | Cómo se comprueba |
|---|---|---|---|---|
| B0.1 | Mergear el trabajo del backend | PR de `feat/asistente-ia` a `main` | Revisar el diff, aprobar, merge. Render redepliega solo (`autoDeploy: true`) | `GET https://conectacomuna-backend-1fl5.onrender.com/api/salud` responde JSON con `estado: ok` |
| B0.2 | Mergear el trabajo del frontend | PR de `fix/ubicacion-fuera-de-cali` a `main` | Igual, con revisión del diff. Son 28 commits, conviene leerlos por bloques de fecha | Vercel marca el deploy en verde y la home carga |
| B0.3 | Enganchar la API | Panel de Vercel | Variable `VITE_API_URL=https://conectacomuna-backend-1fl5.onrender.com` en Production y Preview, y redeploy | Ver B0.4 |
| B0.4 | Semáforo de despliegue | A mano, 5 minutos | Abrir la app publicada, mandar al asistente una pregunta abierta ("busco quién arregle una silla coja") y mirar la pestaña Red | La llamada a `/api/ai/chat` devuelve `application/json` y un texto distinto al del motor local |

Esfuerzo: 2 h. Bloqueante para todo lo demás, porque sin esto se prueba sobre código viejo.

---

## Bloque 1. Que el asistente deje de inventar

Es el hallazgo más caro para el concurso. El motor local devuelve negocios con nombre y WhatsApp inventados y un pacto con código falso, sin ninguna marca de ejemplo. Como la producción usa ese motor, es exactamente lo que ve el jurado.

| ID | Tarea | Dónde | Cómo se arregla | Cómo se comprueba |
|---|---|---|---|---|
| B1.1 | Quitar los talleres inventados | `frontend/src/services/aiService.ts:153-181` y `:745-773` | Borrar los dos arrays. Ese camino devuelve un mensaje sin tarjetas y el botón "Ver oficios cerca", que ya existe | Preguntar "oficios cerca" sin ubicación y "talleres del piloto": ninguna respuesta menciona un negocio concreto |
| B1.2 | Quitar el pacto fabricado | `aiService.ts:566-607` | Borrar el objeto `acuerdo` con `CC-ORD-1`. En su lugar, explicar el tope del 50% y llevar a `/trato-seguro` con un botón | "Quiero pactar un trato seguro" no muestra código ni taller |
| B1.3 | Lo mismo en el backend | `backend/src/routes/ai.js:534-571` | Misma poda. El prompt del sistema ya recibe los negocios reales desde `obtenerNegociosComuna()`, así que el modelo puede nombrarlos sin inventar | Con `GEMINI_API_KEY` puesta, tres preguntas seguidas no nombran negocios que no estén en la tabla |
| B1.4 | Marcar los ejemplos que se conserven | Tarjetas del chat, `AsistenteComunalModal.tsx` | Si quedan comprobantes de ejemplo (`orderService.ts:39,64,88`), la tarjeta pinta una franja "Ejemplo, no es un pedido real" cuando `ejemplo === true` | El modal muestra la franja en `CC-DEMO2026` |

Esfuerzo: 3 h. Después de esto, la frase de la defensa cambia de "el asistente busca en la base" a algo que se puede sostener.

---

## Bloque 2. Cerrar el rol facilitador y la moderación

Hoy se puede llegar a facilitador por dos caminos que nadie diseñó: poniendo `account_type: "facilitador"` en el metadata del registro (el trigger lo copia sin validar) y por `POST /api/auth/registro`, que lo acepta a propósito. Con esa cuenta, `backend/src/routes/reports.js:12` deja leer todos los reportes con nombre y teléfono de quien denuncia, y `:62` deja modificarlos.

| ID | Tarea | Dónde | Cómo se arregla | Cómo se comprueba |
|---|---|---|---|---|
| B2.1 | Validar el tipo en el alta | `frontend/supabase/migracion-rol-facilitador.sql` (nueva) | Reescribir `handle_new_user` para que solo admita `business` y `client`; cualquier otro valor entra como `client`. El trigger `proteger_tipo_cuenta` sigue cubriendo el update | Registrarse con metadata `facilitador` y comprobar en `profiles` que quedó `client` |
| B2.2 | Sacarlo de la API | `backend/src/routes/auth.js:7` | `ACCOUNT_TYPES = ["client", "business"]`. Y de paso `email_confirm: false` y mensaje de error genérico | `POST /api/auth/registro` con `facilitador` devuelve `client` |
| B2.3 | Moderación al equipo | `backend/src/routes/reports.js:9-14` y `:59-70` | Cambiar el chequeo de `account_type` por consulta a `equipo_conecta`. Con `service_role` no hay `auth.uid()`, así que se consulta la tabla por `profile_id` | Con una cuenta de cliente y con una de facilitador, `GET /api/reports` devuelve 403 |
| B2.4 | Decidir qué pasa con las rutas muertas | `auth.js`, `businesses.js`, `reviews.js`, `reports.js` | Recomiendo borrarlas y quitar sus montajes en `server.js`. Son 341 líneas que nadie llama y una de ellas tiene el hueco de permisos. Queda un backend de tres rutas: `salud`, `ai/chat`, `orders/verificar` | `npm start` levanta; los tres endpoints responden; el resto da 404 JSON |

Sobre B2.4: si prefieren conservarlas para la defensa ("así se ve una API completa"), entonces B2.3 es obligatorio y además hay que arreglar `businesses.js` con los mismos criterios. Mantener código muerto con un hueco de permisos es la peor combinación posible.

Esfuerzo: 2 h con B2.4, 1 h sin ella.

---

## Bloque 3. Que no se caiga ni se deje engañar

| ID | Tarea | Dónde | Cómo se arregla | Cómo se comprueba |
|---|---|---|---|---|
| B3.1 | Manejador de errores | `backend/src/middleware/errores.js` (nuevo) y `server.js` | Envolver las rutas async y montar el manejador de cuatro argumentos, más un 404 JSON para `/api`. Sin esto, un fallo de red en Supabase tira el proceso | Cortar la red del backend y hacer una petición: responde 500 JSON y el proceso sigue vivo |
| B3.2 | CORS por subcadena | `server.js:39` | Cambiar `includes("vercel.app")` por `/^https:\/\/[a-z0-9-]+(\.[a-z0-9-]+)*\.vercel\.app$/i`. Y quitar `credentials: true` (`server.js:47`), que no hace falta porque la sesión viaja en el header | `curl -H "Origin: https://x.vercel.app.atacante.io"` recibe error de CORS |
| B3.3 | La salud no valida nada | `frontend/src/lib/apiClient.ts:57-66` | Exigir `content-type: application/json` y `estado === 'ok'` | Con `VITE_API_URL` vacía, la función devuelve `false` aunque el host conteste un HTML 200 |
| B3.4 | Peticiones sin reloj | `apiClient.ts` | `AbortSignal.timeout(12000)` en `apiFetch`, y 60 s solo para el chat, que despierta Render | Apagar el wifi a mitad de una pregunta: el chat avisa en vez de quedarse girando |

Esfuerzo: 3 h.

---

## Bloque 4. Privacidad de `profiles`

Cualquier usuario con sesión descarga nombre, teléfono y barrio de todo el mundo (`schema.sql:48-55`). A `anon` le revocaron columnas; a `authenticated` no. Es el hallazgo P1 que la auditoría del 28 de octubre dejó abierto.

| ID | Tarea | Dónde | Cómo se arregla |
|---|---|---|---|
| B4.1 | Cerrar la tabla | `supabase/migracion-perfiles-privados-v2.sql` (nueva) | `revoke select on public.profiles from authenticated`, luego `grant select (id, full_name, avatar_url, account_type, neighborhood)`, que son justo las que necesita cualquier embed de PostgREST. Política nueva `profiles_select_own` para la fila propia |
| B4.2 | Devolver el perfil propio | Misma migración | `create function public.mi_perfil()` con `security definer` y `grant execute to authenticated`. La fila completa (con teléfono) solo sale por ahí |
| B4.3 | Ajustar el frontend | `frontend/src/services/profileService.ts` | Leer el perfil propio con `rpc('mi_perfil')` en vez de `select('*')`. Si el onboarding hace `.update().select()`, quitarle el `select()` o cambiar el retorno |
| B4.4 | Probar antes en un proyecto de prueba | Supabase, proyecto gratis | Aplicar la migración en un proyecto vacío y correr los ocho flujos de la lista de regresión antes de tocar producción |

Los ocho flujos que hay que repetir después de cualquier migración: registro nuevo, onboarding, editar perfil propio, ver ficha con reseñas, crear pedido, aceptar pedido, panel de reportes del equipo, panel del facilitador.

Esfuerzo: 5 h, casi todo en la prueba. Si el tiempo aprieta, esto se puede dejar como está y anotarlo como riesgo aceptado, pero entonces conviene que la respuesta preparada para el jurado lo diga.

---

## Bloque 5. Bajar el peso del código

No es cosmético: el asistente de 1.374 líneas entra en el bundle inicial y se descarga en un celular de gama baja antes de que el usuario toque nada.

| ID | Tarea | Dónde | Cómo se arregla | Esfuerzo |
|---|---|---|---|---|
| B5.1 | Partir el asistente | `AsistenteComunalModal.tsx` | Extraer `useAsistenteChat` (estado, envío, historial), `MensajeBurbuja` y una tarjeta por `tipo`. Montarlo con `lazy` desde `AppLayout` | 5 h |
| B5.2 | Partir la ficha | `BusinessDetailPage.tsx` | Sacar `RequestForm` (686-913) y `SolicitudEnviadaModal` (916-1042) a su carpeta | 3 h |
| B5.3 | Sacar los modales del pedido | `OrderCard.tsx` | Tres modales copiados (374-546) a componentes propios | 2 h |
| B5.4 | Una sola regla del 50% | `lib/utils.ts` | Función `anticipoPermitido(precio)` y usarla en `OrderCard`, `TratoSeguroVerificationPage` y el asistente. La base ya la valida; esto evita que la UI diga otra cosa | 1 h |
| B5.5 | Dependencias y CSS | `package.json`, `index.css:2` | Borrar `@tanstack/react-query`, `zustand` y `@animateicons/react`, que no se importan en ningún archivo. Mover el CSS de Leaflet al chunk del mapa | 1 h |
| B5.6 | Peticiones que se pisan | `hooks/useAsync.ts`, `BusinessFiltersBar.tsx:88` | `AbortController` en `useAsync` y debounce de 250 ms en el buscador | 2 h |

Si el tiempo no alcanza, el orden de valor es B5.1, B5.6 y B5.5; el resto puede esperar al 14 de noviembre.

Esfuerzo: 14 h.

---

## Bloque 6. Pruebas y CI

Hoy hay cero archivos de prueba y cero integración continua. En un concurso con 15 puntos de metodología y 15 de herramientas, es la ausencia más difícil de explicar.

| ID | Tarea | Cómo se arregla | Esfuerzo |
|---|---|---|---|
| B6.1 | Ocho pruebas con sentido | `vitest` en el frontend. Cubrir `distanceKm`, `getBusinessOpenStatus`, la regla del 50%, `ORDER_TRANSITIONS`, el formateo del código `CC-` y `estaEnZonaPiloto`. Son funciones puras, sin mocks | 3 h |
| B6.2 | CI mínimo | `.github/workflows/verificar.yml` en el repo del frontend: `npm ci`, `tsc -b`, `oxlint`, `vitest run`. Veinte líneas | 1 h |
| B6.3 | Arreglar el warning | `BusinessDashboard.tsx:153` | Sacar `Date.now()` del render a un `useMemo` con dependencia de la fecha, o calcular el vencimiento en el handler | 30 min |
| B6.4 | Probar el backend | `node --test` sobre `calcularDistanciaMetros`, `extraerJsonValido` y el parseo del código de verificación. Obliga a exportar esas funciones de `ai.js` y `orders.js` | 2 h |

Esfuerzo: 6 h.

---

## Bloque 7. Documentación y rúbrica

Es donde el proyecto tiene más hueco relativo. La ficha de entrevista es una plantilla vacía mientras el backlog afirma que hay cuatro fichas de campo.

| ID | Tarea | Qué se hace | Esfuerzo |
|---|---|---|---|
| B7.1 | Fichas reales | Diligenciar cinco fichas con nombre, negocio y cita textual. Con foto si se puede. Si no se consiguen, corregir el backlog y no afirmar lo que no hay | 4 h |
| B7.2 | Ceremonias | Escribir `scrum/sprint-0.md` a `sprint-4.md` con planificación, review y retrospectiva fechadas, más un `sprint-final.md` del 4 de octubre al 13 de noviembre | 5 h |
| B7.3 | Cronograma | Reescribir el de `DOCUMENTACION-GENERAL.md` y `scrum/README.md` para que llegue al 13 de noviembre y diga la verdad: cinco sprints más un tramo de cierre | 1 h |
| B7.4 | El 92,2 | Adjuntar la comunicación oficial como imagen o PDF, o dejar de citarlo en los documentos. Una cifra sin respaldo en un documento que el jurado lee es peor que no tenerla | 1 h |
| B7.5 | Basura de codificación | Quitar los dos caracteres chinos de `Plan-Maestro-ConectaComuna.md:170` y `:477`, arreglar `DECISION-ALCANCE-FULLSTACK.md` en Latin-1 y "ProducciA3n" en `EVIDENCIA-Despliegue-MVP.md` | 1 h |
| B7.6 | Roles | Nombrar Product Owner y Scrum Master en `scrum/README.md` (hoy los cinco están en TBD), quitar el ADMIN del Plan Maestro y alinear el rol del facilitador con la regla de que no media reportes | 1 h |
| B7.7 | Manos limpias | Sacar `docs/` de los dos repositorios (`git rm -r docs`), porque está versionado y la regla propia lo prohíbe. Dejar una sola copia del esquema y borrar `ANALISIS-TECNICO-CODEBASE.md` o reescribirlo, porque describe rutas, archivos y versiones que no existen | 2 h |
| B7.8 | Una sola fuente | Dejar de copiar `docs/` a `vault/` byte a byte (41% de los 102 documentos son copia exacta). El vault enlaza a `docs/` con wikilinks; lo que solo vive en el vault se mueve a `docs/` | 2 h |

Esfuerzo: 17 h. Es el bloque que se puede hacer en paralelo sin tocar código, y el que más puntos mueve por hora invertida.

---

## Bloque 8. Ensayo y congelamiento

| ID | Tarea | Cómo se hace |
|---|---|---|
| B8.1 | Ensayo en producción, con red mala | Abrir la app en un celular con datos móviles, recorrer los cinco puntos del criterio de éxito, cronometrar. Render duerme a los 15 minutos: medir cuánto tarda la primera respuesta del asistente |
| B8.2 | Contingencias por escrito | Tres escenarios con qué se hace: sin wifi en el evento, Render dormido, Gemini sin cupo. El asistente tiene motor local honesto y el QR lo resuelve Postgres, así que hay margen |
| B8.3 | Congelamiento | Desde el miércoles 11 de noviembre, `main` solo recibe arreglos de una línea. Nada de refactors ni migraciones hasta después de la final |
| B8.4 | Guion con las preguntas difíciles | Las de `HUECOS-ARGUMENTATIVOS.md` más tres nuevas: quién modera los reportes, qué pasa si el emprendedor no entrega, y por qué el asistente a veces responde sin internet |

Esfuerzo: 4 h.

---

## Calendario

| Fecha | Bloque | Quién |
|---|---|---|
| Vie 30 oct | B0 y B1 | Luis (deploy) y Sebastián (asistente) |
| Sáb 31 oct | B2 y B3 | Luis y Sergio |
| Lun 2 nov (festivo) | B7.2 a B7.6 | Luis, en casa |
| Mar 3 nov | B4 y regresión | Luis |
| Mié 4 nov | B5.1 | Sebastián |
| Jue 5 nov | B5.2 y B5.3 | Sebastián |
| Vie 6 nov | B5.4 a B5.6 | Sebastián |
| Sáb 7 nov | B6 | Sergio |
| Dom 8 nov | B7.1 | Luis, en campo |
| Lun 9 nov | B7.7, B7.8 y B7.1 si falta | Luis |
| Mar 10 nov | B8.1 y B8.2 | Los tres |
| Mié 11 nov | Congelamiento, solo hotfix | Luis |
| Jue 12 nov | Viaje y ensayo presencial | Los tres |
| Vie 13 nov | Final | Los tres |

Total: unas 53 horas de trabajo repartidas en 13 días. Con tres personas son unas 18 horas cada uno, y B7 corre en paralelo con B4 a B6.

---

## Lo que se acepta y no se toca antes del 13

Cada uno con su razón, para no confundirlo con olvido:

- **El bucket de fotos de pedidos sigue público.** Pasarlo a privado exige firmar URLs y tocar pedidos viejos. Quien tenga el enlace abre la foto; ya no se puede listar.
- **No hay recaudo ni retención de dinero.** La app lo dice en `TratoSeguroPage.tsx:163`, así que la respuesta a "¿protegen mi anticipo?" ya está escrita y es verdad.
- **El pedido se cierra de un solo lado.** El cliente no confirma la entrega. Arreglarlo toca la máquina de estados y no cabe en dos semanas.
- **Tres copias del esquema.** Unificarlas es media hora, pero hay que decidir cuál es la buena y volver a probar todo. Se hace el 14.
- **Sin migración completa de la capa de datos.** React Query está instalado y sin usar; migrar doce fetch sueltos dos semanas antes de la final es cómo se rompe una demo.
- **Sin i18n y sin PWA.** Fuera del alcance declarado.

---

## Riesgos del plan

1. **Los 28 commits del frontend chocan al mergear.** Mitigación: mergear primero el backend, que son 8, y hacer el del frontend con calma el 30 por la mañana, no el 12 de noviembre.
2. **La migración de `profiles` rompe el onboarding.** Mitigación: proyecto de prueba primero y los ocho flujos de regresión. Si algo falla, se revierte la migración sola.
3. **La documentación se come el tiempo del código.** Mitigación: B7 tiene dueño y fechas fijas. Si el 9 de noviembre B7.1 no está, se corrige el backlog en vez de inventar fichas.
4. **El deploy deja la demo peor que antes.** Mitigación: el ensayo del B8.1 con la app publicada, y la etiqueta de Vercel para volver al deploy anterior si algo falla.

---

## Después de la final, en orden

1. Unificar el esquema en un solo archivo con migraciones fechadas y versionadas.
2. Un motor de asistente, en el backend, con pruebas. Borrar el del navegador.
3. Migrar la capa de datos a React Query con `AbortSignal` y claves de caché.
4. Tipos generados desde Supabase (`database.types.ts`) y fuera los 44 `as`.
5. Confirmación de entrega por parte del cliente y un botón de disputa por pedido.
6. Bucket privado con URLs firmadas.

---

## Avance al 29 de octubre, primera pasada

Hecho, y comprobado con las herramientas:

| Bloque | Estado | Qué quedó |
|---|---|---|
| 1. Asistente honesto | Hecho | Tres negocios inventados y dos pactos fabricados fuera de `aiService.ts`; el pacto del backend también. La copia ya no promete códigos que no existen |
| 2. Facilitador | Hecho, menos B2.4 | Migración `migracion-rol-facilitador.sql` escrita. `ACCOUNT_TYPES` sin facilitador, correo sin confirmar automático, error genérico. Reportes solo para `equipo_conecta` |
| 3. No caerse | Hecho | `middleware/errores.js`, 404 JSON, CORS anclado sin credenciales, salud que valida el cuerpo, reloj de 12 s en el cliente |
| 5. Peso del código | Parcial | Regla del 50% en `lib/reglas.ts` y dependencias muertas fuera. Falta partir los componentes |
| 6. Pruebas y CI | Hecho | 10 pruebas de frontend con vitest, 6 de backend con `node --test`, workflow `.github/workflows/verificar.yml` |

Comprobaciones corridas después de los cambios:

- `tsc -b`: 0 errores.
- `oxlint src`: 0 warnings en 90 archivos.
- `vite build`: compila en 2,66 s.
- `vitest run`: 10 de 10.
- `node --test`: 6 de 6.
- Servidor levantado: `/api/salud` responde JSON, `/api/loquesea` responde 404 JSON, y con `NODE_ENV=production` el origen `https://x.vercel.app.atacante.io` ya no recibe cabecera CORS mientras que `https://preview-abc.vercel.app` sí.

Pendiente, con motivo:

- ~~B2.4, borrar las rutas sin consumidor.~~ Cerrado en la tercera pasada.
- **B4**, privacidad de `profiles`. La migración toca el onboarding y hay que probarla en un proyecto de Supabase vacío, no en producción a dos semanas de la final.
- **B5.1 a B5.3**, partir el asistente, la ficha y los modales. Es trabajo de 10 horas y no cabe en esta pasada.
- **B7 y B8**, documentación y ensayo. Van después del código.
- **B0**, mergear y desplegar. Necesita acceso a GitHub y a los paneles de Render y Vercel.

Nada de esto está commiteado todavía: los dos repositorios ya tenían trabajo sin guardar cuando empezó la sesión (`ai.js`, `schema.sql` y `orders.js` en el backend; quince archivos en el frontend), y mezclarlo en un commit habría ocultado de quién es cada cambio.

### Segunda pasada, 29 de octubre por la tarde

- El asistente salió del paquete inicial: ahora es un chunk de 60 KB que se descarga al abrirlo. Leaflet también dejó de viajar en la primera visita, 160 KB de JS y 15 de CSS menos, porque la regla de chunks atrapaba react-leaflet dentro del paquete de React. La primera carga pasó de unos 1.100 KB a 886 KB.
- "Talleres del piloto" ya entra por la rama correcta en los dos motores, y el del navegador muestra los talleres reales que recibe.
- La cascada de Gemini bajó a 6 s por modelo para caber dentro de los 20 s que aguanta el navegador.
- Los tres modales del pedido salieron de OrderCard, que pasó de 557 a 384 líneas, a AceptarPedidoModal, CancelarPedidoModal y NotaAvanceModal.
- B7.5 cerrado: fuera los dos caracteres chinos del Plan Maestro, en docs y en el vault, y EVIDENCIA-Despliegue-MVP reescrita porque estaba con la codificación destrozada.

### Tercera pasada, 29 de octubre, cierre del día

- B2.4 cerrado. Se borraron los cuatro routers sin consumidor (`auth`, `businesses`, `reviews`, `reports`), 434 líneas, más `config/auth.js` y el limitador de escritura que quedaban huérfanos. El backend son tres rutas: salud, verificación del pacto y asistente.
- `DOCUMENTACION-BACKEND.md` reescrita de 375 a 84 líneas y sincronizada en las cuatro copias (docs, backend/docs, frontend/docs y vault).
- `ARQUITECTURA-BACKEND.md` reescrita: describía tablas de agosto (`perfiles`, `productos`, `solicitudes`) que no existen. Ahora tiene la tabla de dónde se valida cada regla, que es lo que conviene tener a mano en la defensa.

### B4 cerrado contra un proyecto de Supabase real

Se levantó un proyecto de prueba vacío y se corrió todo el procedimiento:

1. `schema.sql` completo desde cero: 165 sentencias, 9 tablas, 29 políticas. Ese paso respondió una duda vieja: el archivo sí corre limpio en un proyecto nuevo.
2. Dos usuarios de prueba, uno leyendo el perfil del otro. Antes: `select phone from profiles where id = <el otro>` devolvía el teléfono. Después: `permission denied` (42501).
3. Lo que no se podía romper, no se rompió: el nombre y la foto siguen legibles, la reseña sigue mostrando a su autor, la ficha del negocio sigue mostrando al dueño y el panel del equipo sigue viendo quién reportó.
4. El camino de escritura de la app quedó comprobado por REST: `PATCH /profiles?select=id` responde 200 con la llave y `mi_perfil()` devuelve la fila completa con el teléfono actualizado.

Dos cosas salieron en el camino y quedaron arregladas:

- El panel del equipo pedía el teléfono del reportante en el embed (`businessService`, modo real). Con la migración eso habría dejado el panel en 403. Se quitó del `select`, del tipo y de los datos de demo; la pantalla nunca lo usaba.
- `schema.sql` no se podía aplicar dos veces: el bucket de Storage y las cuatro políticas de `storage.objects` fallaban por duplicado. Ahora el insert del bucket lleva `on conflict (id) do nothing` y el archivo se puede volver a correr sin reventar en ese punto. Las políticas de Storage siguen necesitando borrarse a mano si se reaplica; queda anotado.

### En producción, y el error que costó una hora

Se aplicó la migración en producción antes de desplegar el frontend. El frontend publicado seguía haciendo `select('*')` sobre `profiles`, recibió 403 y nadie podía entrar: la sesión de Supabase sí abría, pero la app moría leyendo el perfil. Ni con correo ni con Google.

Se resolvió desplegando: commit `5fa8b72`, merge a `main` y Vercel publicó un bundle que ya llama a `mi_perfil()`. Comprobado en el sitio publicado: el paquete pasó de 975 KB a 752 KB y contiene la función nueva. El backend también quedó en `main` (`a3976a7`) y Render sirve las tres rutas: `/api/reports` responde 404.

La regla que faltaba quedó escrita en `REGLA-ORDEN-DESPLIEGUE.md`: cerrar permisos va **después** del despliegue, al revés que agregar columnas. Y el plan B de treinta segundos: `grant select on public.profiles to authenticated;` para desbloquear mientras se publica.

### Cuarta pasada, la noche del 29

- **B5.2 cerrado.** `BusinessDetailPage.tsx` pasó de 1.043 a 682 líneas. Salieron `RequestForm` (240 líneas) y `SolicitudEnviadaModal` (133) a `components/business/`. El chunk de la ficha quedó en 49 KB.
- **B7.7 cerrado.** `docs/` fuera de los dos repositorios (`git rm -r docs`, seis archivos), una sola copia del esquema (`frontend/supabase/schema.sql`), y `backend/README.md` ahora apunta ahí en vez de a un `schema.sql` que ya no existe. Se borraron también `aplicar-completo.sql` y `backend/schema.sql`, 1.258 líneas duplicadas.
- **B7.8 cerrado** con `docs/_sync/sincronizar-vault.mjs`: `docs/` es la fuente y el vault es la copia que se abre en Obsidian. Primera corrida: 17 documentos copiados, 32 ya al día y 23 notas que solo viven en el vault, respetadas.
- **B7.6 cerrado.** Nombres en la tabla de roles del `scrum/README.md` (PO: Luis Santiago, Scrum Master: Sergio, Dev frontend: Sebastián, Dev backend: Luis), el actor ADMIN del Plan Maestro pasó a ser el Equipo Conecta, y el facilitador dejó de "cerrar reportes" en la matriz de roles. De paso se corrigió el calendario, que seguía cerrando el 3 de octubre.
- **B7.4 cerrado** como pediste: la nota 92,20 se cita con su fuente, el correo de Fedesoft y REDIS, y quedó `docs/evidencias/` con la instrucción de guardar el mensaje original ahí.
- **B8 escrito.** `ENSAYO-Y-CONTINGENCIAS.md`: los cinco flujos que se ensayan, los tres escenarios malos con qué se dice en cada uno, el congelamiento del 11 y el guion de las ocho preguntas difíciles.

Queda B7.1 (fichas de entrevista, que exige salir a campo) y B7.2 (los `sprint-N.md` con planning, review y retrospectiva).

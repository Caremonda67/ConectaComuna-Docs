# Backend de Conecta Comuna

Revisado el 29 de octubre de 2026. Esto reemplaza la versión anterior, que describía tablas y rutas de agosto que ya no existen (`perfiles`, `productos`, `solicitudes`).

## Qué hay montado

PostgreSQL en Supabase con nueve tablas, y un servicio de Express con tres rutas.

El CRUD no pasa por Express: el frontend escribe en Postgres con PostgREST y las políticas de RLS. Express queda para lo que necesita una llave que no puede viajar al navegador.

| Ruta | Para qué |
|---|---|
| `GET /api/salud` | Que Render sepa que el servicio está vivo |
| `GET /api/orders/verificar/:codigo` | Resolver el QR del pacto sin abrir la tabla de pedidos |
| `POST /api/ai/chat` | Asistente con Gemini y motor de respaldo |

El detalle de cada una está en `DOCUMENTACION-BACKEND.md`.

## Las tablas

- `profiles` — una fila por usuario, ligada a `auth.users`. Guarda el tipo de cuenta (`client`, `business`, `facilitador`).
- `businesses` — el negocio, uno por dueño. Incluye catálogo de servicios, venta al por mayor y el estado de verificación.
- `orders` — el pedido o pacto: precio, anticipo, estado y notas del taller.
- `reviews` — reseña de un pedido completado, una por pedido.
- `facilitadores_negocio` — el vínculo entre un facilitador y un negocio, con su estado de aprobación.
- `reportes_comunitarios` — la denuncia de un negocio, con su estado y la nota de quien la resolvió.
- `reporte_respuestas` — la versión de cada parte dentro de un reporte.
- `direcciones_usuario` — direcciones guardadas por el cliente.
- `equipo_conecta` — las cuentas que pueden moderar. Se asignan a mano.

## Dónde se valida cada cosa

Esta tabla es la que conviene tener a mano en la defensa, porque casi todas las reglas del producto viven en la base y no en JavaScript.

| Regla | Dónde vive |
|---|---|
| El anticipo no pasa del 50% | `cambiar_estado_pedido()`, que además valida la transición y el rol |
| Un pedido se califica solo si está completado y es tuyo | Política `reviews_insert_client` |
| El sello territorial lo da un facilitador vinculado | `otorgar_sello_territorial()` |
| Las cuentas de facilitador las asigna el equipo | `handle_new_user` en el alta y `proteger_tipo_cuenta` en la edición |
| La moderación de reportes es del equipo | `es_equipo_conecta()` |
| El QR muestra menos datos a quien no es parte | `verificar_pedido_publico()` según `auth.uid()` |
| Un dueño no puede subirse el rating ni la verificación | `protect_business_columns` |

## Qué se retiró y por qué

El 29 de octubre se borraron cuatro routers de Express (`auth`, `businesses`, `reviews`, `reports`, 434 líneas) que llevaban meses sin consumidor: el frontend ya hacía todo eso contra PostgREST. Mantener dos versiones de las mismas reglas terminó en contradicción, porque la de Express permitía moderar reportes a cualquier cuenta de facilitador mientras la base ya exigía pertenecer al equipo.

La lección para el equipo: antes de escribir un endpoint, comprobar quién lo va a llamar. Si la respuesta es nadie, no se escribe.

## Lo que falta

- Confirmación de entrega por parte del cliente. Hoy el pedido lo cierra el negocio y el cliente no tiene cómo decir que recibió.
- Pasar el bucket de fotos de pedidos a privado con URLs firmadas.
- Pruebas de las funciones de Postgres. Hoy solo hay pruebas de JavaScript puro.

## Convenciones

- `snake_case` en la base, `camelCase` en TypeScript.
- Una carpeta por recurso en `src/routes/`, configuración en `src/config/`.
- Código corto, sin comentarios de más. Las reglas de negocio van en la base.

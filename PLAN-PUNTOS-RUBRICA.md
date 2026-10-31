# Plan para recuperar los puntos que faltan

> **Estado al 31 de octubre.** Los bloques 1 a 4 están cerrados y medidos; lo
> que sigue abajo es el plan tal como se escribió el 30, que conviene conservar
> para ver de dónde salió cada decisión. Dos cosas que este plan afirmaba y
> resultaron falsas, corregidas en [[EVIDENCIA-CONTRASTE-WCAG]]:
>
> - No eran «46 variantes rotas»: la paleta oscura **no es un espejo**, así que
>   32 de ellas eran correctas y solo 19 pares quedaban por debajo del mínimo.
> - Pasar el botón a `brand-700` a secas habría arreglado el tema claro y
>   **roto el oscuro**, donde ese tono es verde claro. Necesita `dark:text-brand-50`.
>
> | Bloque | Estado |
> |---|---|
> | 1 · Contraste accesible | **Cerrado.** 11 rutas, 1 526 elementos, cero fallos en los dos temas. |
> | 2 · Microinteracciones | **Cerrado.** Cargas con forma, acuse al guardar, `prefers-reduced-motion` global. |
> | 3 · Experiencia de usuario | **Cerrado.** Textos del asistente, botón flotante, voz visible en portada. |
> | 4 · Que se vea lo que usa | **Cerrado.** Ruta `/tecnologia`. |
> | 5 · Métricas del piloto | **Omitido por decisión del equipo.** El trabajo de campo sigue siendo lo que falta. |


Fecha: 30 de octubre de 2026. Faltan 14 días para la final.
Punto de partida: 92,20 / 100. Quedan **7,8 puntos** repartidos en seis criterios.

Este plan cubre solo lo que se gana trabajando en la aplicación. Al final hay una lista corta de lo que no se gana programando, para que no se pierda de vista.

---

## Lo que se puede ganar con la aplicación

| Criterio | Falta | ¿Se gana con código? |
|---|---|---|
| Diseño, presentación y estética | 1,6 | Sí, y es medible |
| Experiencia de usuario | 0,8 | Sí |
| Uso de herramientas | 0,8 | Parcial: la app tiene que *dejar ver* lo que usa |
| Solución y resultados | 1,6 | Parcial: la app puede mostrar métricas, los datos los generan ustedes |

**Total alcanzable con trabajo de aplicación: unos 3,5 puntos.** El resto depende de ensayo y documentación.

---

## Bloque 1 — Contraste accesible (diseño, 1,6 pts)

El evaluador escribió "modo oscuro accesible". Eso no es opinión, es una medida: WCAG AA pide 4,5:1 para texto normal y 3:1 para texto grande. Lo medí con un script sobre la aplicación corriendo, en los dos temas.

### Lo que encontré

**Modo claro: está bien.** La navegación inferior da 5,36:1, el pie 10,24:1. Mis primeras mediciones decían otra cosa, pero eran un artefacto de cambiar la clase del tema a mano sin dejar que la aplicación se volviera a pintar. Con recarga limpia, pasa.

**Familia A: variantes `dark:` que pelean con la paleta.** Las cuatro escalas propias (`brand`, `ink`, `cream`, `accent`) se redefinen enteras bajo `html.dark` en `index.css`: en oscuro el 50 es casi negro y el 800 es claro. El token ya se invierte solo, así que escribir `dark:text-brand-300` encima pone texto oscuro sobre fondo oscuro.

```
text-xs text-brand-800 dark:text-brand-300   →  1,97:1   (mínimo 4,5)
```

Quedan **46 ocurrencias en 13 archivos**. Cada una se arregla borrando la variante `dark:`, no agregando nada. Las escalas de Tailwind (`amber`, `emerald`, `rose`) no se invierten y ahí el `dark:` sí hace falta: esas no se tocan.

**Familia B: blanco sobre el verde de marca.** El botón primario es `bg-brand-500 text-white`, que da **2,53:1**, y falla en los dos temas. Es el botón más repetido de la aplicación.

| Tono | Blanco encima | |
|---|---|---|
| `brand-500` #6fb257 | 2,57:1 | no cumple |
| `brand-600` #5a9a45 | 3,42:1 | no cumple |
| **`brand-700`** #467a36 | **5,13:1** | cumple |

### El trabajo

1. **Una línea** en `components/ui/Button.tsx`: la variante `primary` pasa de `bg-brand-500` a `bg-brand-700`. Arregla todos los botones primarios de la aplicación de una vez. Hay que mirar cómo queda el verde más oscuro: es una decisión de marca, no solo técnica.
2. **Borrar las 46 variantes** `dark:text-{brand|ink|cream|accent}-N`. Archivo por archivo, midiendo después de cada pantalla.
3. **Volver a medir** las siete pantallas públicas en los dos temas y dejar la tabla antes/después como evidencia.

**Esfuerzo:** medio día. **Riesgo:** bajo, son borrados y un cambio de tono.
**Evidencia para el jurado:** una tabla con el contraste antes y después. Es el tipo de dato que convierte "nos gusta el diseño" en "cumple WCAG AA".

---

## Bloque 2 — Microinteracciones (diseño, parte del mismo 1,6)

La otra mitad de lo que pidió el evaluador. La aplicación ya tiene bastante: `hover:-translate-y-0.5`, `active:scale-95`, el confeti al copiar el enlace, la animación del sello.

Lo que falta no es agregar más, es que **respondan a algo que importa**:

- **Estados de carga con forma.** Hoy varias pantallas muestran "Buscando…" en texto. Los `CardSkeletonList` ya existen: usarlos en los sitios donde todavía no están.
- **Confirmación visible al guardar.** Cuando el facilitador sella un negocio o el emprendedor guarda un servicio, la confirmación es texto. Un cambio de estado visible (el sello que aparece, la tarjeta que se mueve de columna) se lee mejor y se ve mejor en video.
- **Respetar `prefers-reduced-motion`.** Ya se usa `useReducedMotion` en el asistente y en el sello. Revisar que esté en todas las animaciones: forma parte de "accesible".

**Esfuerzo:** medio día. **Riesgo:** bajo.

---

## Bloque 3 — Experiencia de usuario (0,8 pts)

El evaluador pidió "resaltar facilidad para personas no letradas digitalmente". Hay tres cosas concretas pendientes de las auditorías anteriores:

1. **"Ir a la sección" no dice nada.** Cuando el asistente propone navegar, la tarjeta dice "Sección recomendada / Toca el botón para abrir esta sección directamente / Ir a la sección". Para el público del proyecto eso no informa. Debe decir a dónde va: "Ir a mis pedidos", "Ir a crear mi negocio".
2. **El botón flotante del asistente tapa contenido** en celular: en la portada cubre parte de la primera tarjeta de categoría.
3. **El encabezado del asistente se corta** como "Asistente Co…" en pantallas de 375 px.

Y uno más de fondo: la aplicación tiene lectura por voz de las respuestas (`Escuchar respuesta`) y dictado por micrófono. **Eso es exactamente lo que el criterio premia y hoy está escondido.** Que se note en la primera pantalla, no solo dentro del chat.

**Esfuerzo:** medio día. **Riesgo:** bajo.

---

## Bloque 4 — Que se vea lo que usa (herramientas, 0,8 pts)

El evaluador pidió "destacar React 19, Supabase RLS, Express y Leaflet". Eso no se gana escribiendo más código, se gana **mostrando el que ya hay**. La aplicación tiene cosas que no se ven si nadie las señala:

- **RLS de verdad**, con políticas por fila y permisos por columna. Se puede demostrar en vivo: pedir el teléfono de alguien sin sesión y enseñar el `42501`.
- **Funciones en la base** con máquina de estados (`cambiar_estado_pedido`), no lógica suelta en el cliente.
- **Tiempo real** de Supabase en el panel del negocio.
- **Leaflet** con selección de punto y geocodificación contra OpenStreetMap, que resuelve cualquier lugar de Colombia.
- **Pruebas y CI** en cada push.

**El trabajo es una pantalla, no un refactor:** una ruta `/tecnologia` o una sección en "Cómo funciona" que explique la arquitectura en lenguaje llano, con un diagrama. Sirve para el jurado y para la documentación.

**Esfuerzo:** medio día (la skill `archify` del catálogo genera el diagrama).

---

## Bloque 5 — Métricas del piloto (resultados, 1,6 pts)

Aquí la aplicación ayuda, pero **no sustituye los datos**. Hoy hay 4 pedidos reales y 4 reseñas, y los contadores ya son honestos, así que esa cifra es la que verá el jurado.

**Lo que la aplicación puede aportar:** una pantalla de métricas reales del piloto, calculadas desde la base, no escritas a mano. Emprendedores activos, pactos cerrados, valor total movido, tiempo medio de respuesta, barrios cubiertos. Con el dato de hoy se ve pequeño; con el de dentro de dos semanas, no.

**Lo que no puede:** generar los pactos. Eso es trabajo de campo.

**Esfuerzo:** medio día de pantalla, **catorce días de piloto**.

---

## Orden sugerido

| Día | Trabajo | Puntos que toca |
|---|---|---|
| 1 | Contraste: botón primario + las 46 variantes | Diseño 1,6 |
| 2 | Volver a medir y dejar la tabla de evidencia | Diseño |
| 3 | Microinteracciones y `prefers-reduced-motion` | Diseño |
| 4 | UX: textos del asistente, botón flotante, voz visible | UX 0,8 |
| 5 | Pantalla de arquitectura con diagrama | Herramientas 0,8 |
| 6 | Pantalla de métricas del piloto | Resultados 1,6 |
| 1–14 | **Piloto real, en paralelo, todos los días** | Resultados 1,6 |

---

## Lo que no se gana programando

Son **2,8 puntos** y conviene decirlo claro:

- **Metodología (1,4).** Pidieron sprints, retrospectivas y métricas del proceso. Es documentación en `docs/scrum/`, y ya existe parte.
- **Dominio del tema (1,0).** Se gana respondiendo sin dudar en los 5 minutos de preguntas. Se gana ensayando.
- **Expresión oral (0,4).** El evaluador pidió explícitamente no leer. Se gana ensayando.

Los 10 minutos cronometrados más 5 de preguntas no los ha ensayado nadie todavía. Es el trabajo de mayor retorno por hora que queda, y no aparece en ningún plan de código.

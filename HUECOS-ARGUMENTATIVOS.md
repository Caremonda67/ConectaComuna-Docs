# Huecos argumentativos: dónde nos pueden acorralar

Fecha: 29 de octubre de 2026. Faltan 15 días para la final.
Esto no es una auditoría de código. Es la pregunta de si la aplicación, contada en voz alta ante un jurado, se sostiene: qué promete, qué entrega de verdad, y dónde el relato se queda sin respuesta.

Método: recorrer el flujo completo probándolo, y después preguntarle a cada pieza "¿y si el jurado pregunta esto?".

---

## Antes: lo que ya quedó sólido

Vale la pena decirlo primero, porque cambia el tono de la conversación.

- **El tope del 50% no es una promesa de la interfaz, es una restricción de la base.** `advance_payment <= round(final_price * 0.5, 2)` como CHECK en la tabla. Si alguien pregunta "¿y si alguien se salta el tope?", la respuesta es que la base no deja guardar la fila.
- **El ciclo del pedido tiene máquina de estados.** `cambiar_estado_pedido` define quién puede mover qué: solo el taller acepta, solo el taller marca en proceso y terminado, cualquiera de los dos cancela. No hay forma de alterar un pacto ya registrado editando la fila.
- **El cliente no puede pedirse a sí mismo.** La política de inserción exige `auth.uid() <> owner_id` del negocio, y fuerza que el pedido nazca en `pending`, sin precio final ni anticipo.
- **El equipo de Conecta Comuna no se puede auto-asignar.** `equipo_conecta` solo tiene política de lectura: entrar a la mesa de moderación requiere escribir la fila desde la consola.

Eso es un cimiento real. Los huecos de abajo son del relato, no de la ingeniería.

---

## Nivel 1: pueden acorralarnos en vivo

### 1. El emprendedor elige quién lo verifica

Es el más grave, y es estructural.

El dueño genera un código de seis dígitos en su panel y se lo da a quien quiera. Quien lo use queda vinculado como facilitador aprobado, y a partir de ahí puede otorgarle el sello "Verificado en territorio".

`vincular_facilitador_por_codigo` comprueba que la cuenta sea de tipo facilitador y que el código exista. **No comprueba que el facilitador no sea el propio dueño.** Entonces:

> Me registro con una segunda cuenta marcando "facilitador", uso mi propio código de apadrinamiento, me vinculo a mi negocio, y me otorgo el sello de verificación.

Ni siquiera hace falta la segunda cuenta: basta un primo. La pregunta que lo destapa es una sola: **"¿quién verifica al que verifica?"**

**Qué hacer, en dos capas:**

*Lo inmediato (media hora):* que la función rechace `facilitador_id = owner_id`. Cierra el caso descarado, que es el que alguien probaría en vivo.

*Lo que de verdad responde la pregunta (es de discurso, no de código):* dejar de presentar el sello como un certificado y presentarlo como lo que es, **el testimonio de un vecino con nombre propio**. La base ya guarda `verification_by` y `verification_date`. Mostrarlo en la ficha:

> "Verificado el 12 de octubre por Ana Ruiz, facilitadora comunitaria."

Un certificado anónimo se falsifica sin costo. Un testimonio firmado tiene un costo social: si Ana avala un taller que estafa, Ana queda señalada en el barrio. Esa es la respuesta honesta y además es más bonita que la que teníamos.

### 2. Las insignias no salen de la actividad real

"Maestro de la comuna" se otorga con `completed_orders >= 50`. En la base real, Costuras Doña Marta tiene `completed_orders` en **64** y `rating_count` en **4**.

Sesenta y cuatro trabajos terminados y cuatro personas que opinaron. Un jurado que mire la ficha y haga la división pregunta de dónde salen esos números, y la respuesta verdadera es que son semillas que inflamos para que la demostración se viera viva.

Es exactamente el tipo de dato que destruye la credibilidad de todo lo demás: si los números de la portada son de adorno, ¿qué más lo es?

**Qué hacer:** o se bajan las semillas a cifras que el historial de pedidos sostenga, o se separa explícitamente lo que viene de la plataforma de lo que viene de antes ("lleva 12 años cosiendo en el barrio" es cierto y no necesita un contador falso). Lo que no se puede es dejar un contador que la propia base desmiente.

---

## Nivel 2: preguntas incómodas sin respuesta preparada

### 3. "¿Y si el emprendedor no cumple?"

El Trato Seguro registra el acuerdo, le pone código y QR, y fija el tope del anticipo. No mueve plata, no retiene nada y no obliga a nadie. Si el taller incumple, lo que ofrece la plataforma es que el vecino reporte y el equipo revise.

Eso está bien, **mientras lo digamos así**. El riesgo es el nombre: "Trato Seguro" suena a seguro, y la palabra "garantía" aparece en la página. Si un jurado pregunta quién devuelve la plata y la respuesta es "nadie, queda el registro", el golpe duele por la distancia entre lo prometido y lo entregado.

**Qué hacer:** ajustar el encuadre, no la función. Es un **comprobante con testigos y un tope acordado**, no un seguro. Dicho de frente suena mejor:

> No retenemos tu plata, no somos un banco ni queremos serlo. Lo que hacemos es que el trato quede escrito, con un tope de anticipo que la plataforma no deja saltarse, y con un código que cualquiera puede verificar. Cuando hay un papel, hay con qué reclamar.

### 4. "¿Por qué un vecino que apadrina al taller ve mi dirección?"

`verificar_pedido_publico` trata como participante del pacto al cliente, al dueño del taller **y al facilitador aprobado**. El facilitador ve el nombre del cliente y la dirección de entrega.

Para mediar un conflicto tiene sentido. El problema es que **el cliente nunca aceptó eso**. Pactó con el taller, no con quien lo acompaña. Es la clase de detalle que choca con la página de privacidad y con la Ley 1581 que citamos nosotros mismos.

**Qué hacer:** decidir y escribirlo. Dos caminos razonables:
- El facilitador ve el pacto sin datos del cliente, y solo accede a ellos si el cliente abre un reporte sobre ese pacto. La mediación se activa por una puerta, no por defecto.
- O se le avisa al cliente al pactar: "este taller tiene un facilitador comunitario que puede ver este acuerdo".

El primero me parece más defendible y es menos trabajo.

### 5. "¿Cuánta gente lo está usando?"

El piloto tiene ocho negocios. Dos tienen cero pedidos finalizados. "Solución y resultados" vale 15 puntos y tenemos 13.4.

No es un hueco de código, es el hueco de la presentación: si el único dato duro son semillas, el criterio no sube. Cualquier cifra real, aunque sea pequeña, vale más que una inventada grande. Tres emprendedores reales usándolo una semana, con capturas y una frase de cada uno, pesa más que "64 servicios finalizados" que nadie puede comprobar.

---

## Nivel 3: coherencia del relato

### 6. El territorio

Ya está desarrollado en [[PLAN-ALCANCE-TERRITORIAL-Y-FACILITADOR]]: la aplicación busca en toda Colombia pero habla de "la comuna" como si fuera un lugar único, y `neighborhood` es texto libre, así que el territorio no existe como entidad. Eso es lo que hace que se sienta de un solo barrio.

---

## Lo que ya se corrigió hoy

**El asistente le decía a cualquiera que un pacto ajeno era suyo.** `pactoDesdePedido` devolvía `nivel: 'participante'` escrito a mano. Sin sesión, pegando `CC-ORD-3` (de otro cliente), el chat respondía:

> "Sí, el acuerdo CC-ORD-3 está asociado a tu cuenta."

Falso dos veces: no había cuenta, y el pacto era de otra persona. Además mostraba el comprobante completo.

La lógica del asistente estaba bien, tenía su rama para no participantes. El fallo venía del servicio. Ahora el nivel se calcula mirando quién pregunta, con el mismo criterio que la función de la base, y un extraño recibe:

> "El acuerdo CC-ORD-3 existe y está registrado con Costuras Doña Marta, por $120.000. Como no participas en ese trato, no puedo mostrarte el nombre ni la dirección del vecino."

Comprobado en el navegador, sin sesión y con sesión ajena.

**La bandeja de reportes no tenía modo oscuro.** Las clases ámbar iban sin su pareja `dark:`, así que las tarjetas salían sobre un gris sucio.

---

## Orden sugerido

| Qué | Cuánto | Por qué primero |
|---|---|---|
| Bloquear que el dueño se apadrine a sí mismo | 30 min | Es la pregunta que destapa todo |
| Mostrar quién verificó y cuándo, en la ficha | 2 h | Convierte el hueco en argumento |
| Cuadrar o explicar los contadores de reputación | 1 h | Un dato que la base desmiente |
| Reescribir la promesa del Trato Seguro | 1 h | Cero código, mucho efecto |
| Decidir qué ve el facilitador de un pacto | 2 h | Choca con nuestra propia política |
| Territorio | 1-2 días | Ver el plan aparte |

Lo de arriba son dos días de trabajo. Lo que compra es que ninguna de las cinco preguntas difíciles nos agarre sin respuesta.

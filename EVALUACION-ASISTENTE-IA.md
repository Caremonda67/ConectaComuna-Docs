# Evaluación del Asistente Comunal: qué tan bien funciona y qué se corrigió

Fecha: 29 de octubre de 2026. Faltan 15 días para la final del 13 de noviembre.
Método: usar la aplicación como la usaría un vecino, en vista de celular (375x812), contra la base real de Supabase y el backend Express local. No es una revisión de código: todo lo de abajo se vio en pantalla.

## Resumen

El asistente está bien. Entiende el lenguaje de barrio, no inventa talleres cuando no los hay y el dictado por voz extrae oficio y precio correctamente. Los problemas que aparecieron no eran de inteligencia sino de honestidad y de qué pasa cuando algo falla.

Seis hallazgos, los seis corregidos.

---

## Lo que ya funcionaba bien

**Entiende como habla la gente.** Escribí "necesito que me cojan la basta de un pantalón", sin decir la palabra costura, y recomendó Costuras Doña Marta. Para un emprendedor con poca experiencia digital eso es justo lo que hace falta.

**No inventa.** Pedí un veterinario y respondió que no hay ninguno inscrito, invitando a explorar el directorio. Es la prueba que un jurado técnico va a intentar primero y la pasa.

**El dictado extrae bien.** De "hago arreglos de ropa y cojo bastas, cobro 15 mil" sacó título, descripción y 15.000 COP estructurados en una tarjeta.

**Los tiempos sirven para tarima.** Medidos contra el backend local: 320 ms las respuestas del motor directo, 1,6 s las que pasan por Gemini. Nadie se va a quedar mirando una ruedita.

---

## 1. Decía que había guardado algo que no guardó

Tras dictar el servicio, sin sesión iniciada y sin negocio creado, respondía:

> "Ya registré tu servicio de arreglos de ropa y cogida de bastas por un valor de 15 mil pesos. Queda perfecto para que los vecinos te encuentren."

Debajo, la misma tarjeta decía "SERVICIO IDENTIFICADO PARA TU CATÁLOGO" y el botón "Crear negocio para publicar este servicio". La tarjeta era honesta y el texto no.

Era el hallazgo más grave: un jurado que haga esa prueba ve a la IA prometiendo algo que no ocurrió.

**Corregido** en la regla 2 del prompt de `ai.js`: se le prohíbe decir que guardó, registró o publicó, se le indica que la tarjeta es un borrador, y se le pide mirar "Negocio propio" en el contexto para explicar el siguiente paso. Ahora responde:

> "Entiendo que haces arreglos de ropa y coges bastas por un valor de 15 mil pesos. Como aún no tienes un negocio creado en la plataforma, te invito a crear tu ficha primero."

## 2. El prompt le pedía a Gemini inventar coordenadas de Cali

En las reglas de ubicación no definida estaba escrito, literal:

```
"ubicacionDetectada": { "barrio": "NombreDelBarrio", "lat": 3.4..., "lng": -76.5... }
```

El frontend guarda ese `ubicacionDetectada` en `localStorage`, así que la IA podía mover a alguien de ciudad sin que lo pidiera y dejarlo fijado toda la sesión. Es el mismo fallo que quitamos del diccionario de barrios, sobreviviendo en el prompt.

**Corregido:** se prohíbe inventar coordenadas y devolver `ubicacionDetectada`. La aplicación resuelve el lugar contra el mapa antes de llegar al backend.

## 3. Los chips de ubicación volvían a ser solo de Cali

Al preguntar por el comercio más cercano sin ubicación, ofrecía "Estoy en El Rodeo / Antonio Nariño / Nueva Floresta". Alguien en Bogotá no tenía ninguna opción válida.

Estaba en cuatro sitios: el prompt de Gemini, el motor de respaldo del backend, el motor del frontend y la rama de GPS negado del modal.

**Corregido** en los cuatro. Ahora los ejemplos son de ciudades distintas ("Estoy en El Rodeo, Cali" y "Estoy en Chapinero, Bogotá") y el texto pide el barrio con la ciudad.

## 4. Sin backend, el asistente no sabía buscar oficios

Apagué el backend para simular Render frío, que es exactamente lo que puede pasar en la final: el plan gratuito duerme a los 15 minutos de inactividad y tarda cerca de un minuto en despertar.

Escribí "necesito una modista" y respondió:

> "Te escucho con atención, vecino. Cuéntame un poco más de lo que necesitas."

El motor local del frontend tiene 24 reglas (saludos, agradecimientos, Trato Seguro, dictado, pedidos) pero **ninguna para buscar un oficio**, que es la pregunta más probable de un jurado. Todas las búsquedas caían en ese genérico.

Peor: las pocas reglas que sí nombraban negocios usaban datos de ejemplo con identificadores `biz-1`, `biz-2`. En la base real los identificadores son UUID, así que tocar "Ver perfil" llevaba a una ficha inexistente.

**Corregido:** se agregó la regla 21b, que reconoce el oficio por las palabras que usa la gente (modista, costurera, basta, chapa, celular, barbería, almuerzo…) y consulta los negocios reales de Supabase antes de responder. Si no hay talleres de ese oficio no inventa: manda al directorio filtrado.

Verificado con el backend caído: "necesito una modista" devuelve Costuras Doña Marta a 204 m, con su WhatsApp, y el enlace apunta a `/negocio/482d4311-3bb7-4e87-9076-7781f63751df`. Identificador real.

## 5. La conversación no bajaba al último mensaje

Medido en el navegador: el contenedor del chat quedaba en 518 píxeles de 1885. Mandabas un mensaje, llegaba la respuesta, y seguías viendo el mensaje anterior. Había que bajar a mano cada vez.

La causa era `scrollIntoView` sobre un centinela al final de la lista, que con `behavior: smooth` y contenido que entra después del primer pintado (las tarjetas de negocio y los chips) no llegaba al fondo.

**Corregido:** se mueve el contenedor directamente con `scrollTo`, repitiendo a los 120 ms para alcanzar las tarjetas que entran tarde.

## 6. Encabezado de una lista vacía

Cuando el asistente respondía con `tipo: "negocios"` pero sin resultados (el caso del veterinario), igual pintaba el título "Negocios recomendados cerca de ti:" seguido de nada.

**Corregido:** el bloque solo se pinta si hay al menos un negocio.

## 7. Suponía el género de quien escribe

Respondía "¡Hola, vecina!" o "¡Qué gran labor, vecina!" sin tener ningún dato sobre el género del usuario. En un proyecto cuya categoría es Inclusión y Bienestar eso se nota.

**Corregido** en el tono del prompt: trato neutro, o el nombre de la persona cuando está en el contexto. Comprobado con tres respuestas seguidas, las tres en neutro.

---

## Detalles menores, sin corregir

- En celular el título del encabezado se corta como "Asistente Co…". Cabe acortarlo o dejar que use dos líneas.
- El botón flotante del asistente tapa parte de la primera tarjeta de categoría en la vista de celular.
- El motor local del frontend y el del backend tienen reglas parecidas pero no idénticas. Mientras ambos existan van a separarse; conviene decidir cuál manda.

## Cómo se probó

- Vista de celular 375x812, `localStorage` limpio en cada escenario.
- Contra Supabase real (`frontend/.env` con las tres variables) y backend Express local.
- Escenarios: primera visita sin ubicación, búsqueda en lenguaje coloquial, oficio inexistente, dictado de servicio sin sesión, pregunta de cercanía sin ubicación, y el recorrido completo con el backend apagado.
- `tsc -b`, `oxlint` y `node --check` limpios después de cada corrección.

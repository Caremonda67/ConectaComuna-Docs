# Ensayo y contingencias para la final

Viernes 13 de noviembre, 7:30 a 1:00, Bogotá. Diez minutos de presentación con demo y cinco de preguntas.

## Lo que se ensaya, y en este orden

1. **Los cinco flujos que un jurado puede tocar.** Directorio con negocios reales, ficha con mapa, creación de un pedido con anticipo del 50%, comprobante por QR abierto sin sesión, y sello territorial en una ficha. Si uno se cae, la demo se cae.
2. **El asistente**, con una pregunta que no sea de las que ya sabemos de memoria: "busco quién me arregle una silla coja" y "talleres del piloto". Cronometrar. Si tarda más de 10 segundos, la respuesta es buena pero la espera se nota.
3. **La pantalla del equipo**, entrando con la cuenta asignada, para mostrar que la moderación no la hace un facilitador cualquiera.
4. **En un celular, con datos móviles**, no solo en el portátil con wifi. La primera carga despierta Render: medir cuánto tarda.
5. **Con la red cortada a mitad de una pregunta**: el asistente debe seguir respondiendo con el motor local.

## Los tres escenarios malos

| Qué pasa | Qué se hace | Qué se dice |
|---|---|---|
| No hay wifi en el salón | Se usa el hotspot del celular de uno de los tres. Si tampoco hay señal, se muestra el guion de video ya grabado, que está en local | "La demo completa quedó grabada por si la red del evento no coopera" |
| Render está dormido y tarda un minuto | Mientras despierta, se muestra la ficha del negocio y el mapa, que no dependen de Render. El asistente tiene motor local | "El asistente responde desde el navegador cuando el servidor está frío; los datos son los mismos" |
| Gemini sin cupo | El asistente cae al motor de reglas y responde igual, con los negocios de la base | "El modelo es una capa; el producto funciona sin él" |

Lo que **no** se puede caer en ningún escenario es Supabase, porque ahí están los datos. Por eso el hotspot va con datos desde el primer minuto, no cuando ya falló.

## Congelamiento

- **Desde el miércoles 11 de noviembre**, `main` solo recibe arreglos de una línea. Nada de refactors, migraciones ni despliegues grandes.
- Las migraciones se aplican **después** de desplegar, como dice `REGLA-ORDEN-DESPLIEGUE.md`.
- El 12 se congela todo: solo se toca si la app no abre.

## Guion para las preguntas difíciles

Las cinco minutos de preguntas es donde se gana o se pierde. Cada respuesta empieza por el dato, no por la excusa.

1. **"¿Quién modera los reportes?"** El equipo de Conecta Comuna, desde `/panel/equipo`. Las cuentas se asignan a mano en `equipo_conecta`. Un facilitador responde por los negocios que acompaña, pero no decide.
2. **"¿Ustedes retienen el dinero?"** No. No hay recaudo ni devolución. El tope del 50% es una regla del pacto que la base valida, y la app lo dice en la página de Trato Seguro.
3. **"¿Y si el emprendedor cobra el anticipo y no entrega?"** Hoy el cliente puede reportar el negocio y el equipo media. No hay un botón de disputa por pedido, y lo tenemos claro: es lo siguiente que toca.
4. **"¿Esos negocios son reales?"** Los del directorio son datos sembrados para el piloto, y lo admitimos. El piloto con la comunidad se hace después de la final; lo que está probado es el flujo completo con datos de prueba.
5. **"¿Por qué un backend si usan Supabase?"** Porque la llave de Gemini no puede viajar al navegador y el QR del pacto no debe abrir la tabla de pedidos. El CRUD va por PostgREST con RLS; la API hace esas dos cosas y dice que está viva.
6. **"¿Qué pasa con los teléfonos de la gente?"** Cualquier usuario con sesión ve nombres pero no teléfonos. El teléfono propio sale por una función que filtra por `auth.uid()`. Las cuentas anónimas solo ven nombre y foto.
7. **"¿Cuántos usuarios reales tienen?"** Ninguno todavía fuera de las pruebas. El número que tenemos es de datos sembrados y no lo presentamos como tracción.
8. **"¿Cómo prueban el código?"** Diez pruebas de reglas en el frontend y seis del motor del asistente en el backend, más tipos y linter en integración continua. No tenemos pruebas de la base.

## Lo que se lleva impreso

- El QR del pacto de ejemplo, para escanearlo con un celular del jurado.
- Un resumen de una página con la arquitectura y las tres rutas de la API.
- Nada de esto se lee en voz alta: es para que quede sobre la mesa.

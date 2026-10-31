# Funcionalidades del Backend (Resumen)

Montamos la lógica trasera en Supabase con Node.js, cubriendo 6 procesos principales:

1. **Autenticación de rol dual:** Una sola sesión que te deja saltar entre "Modo cliente" y "Modo negocio" sin tener que salir ni crear correos dobles.
2. **Cálculos automáticos (Triggers):** La base de datos actualiza por debajo el promedio de estrellas y la cantidad de ventas del negocio apenas entra una calificación nueva.
3. **Filtro geográfico de distancia:** Fórmula que cruza la posición del usuario con la de los locales, devolviendo los negocios que están a "X" kilómetros a la redonda.
4. **Candados de seguridad (RLS):** Reglas nativas en la base de datos que impiden que un tercero borre o edite productos de un negocio que no es suyo.
5. **Generador de apadrinamiento:** Endpoint que crea un código temporal de 6 dígitos para que un facilitador se pueda enlazar a la cuenta de un emprendedor mayor.
6. **Reportes comunitarios:** El vecino reporta un negocio desde su ficha y el caso cae en la bandeja de moderación del facilitador, que lo marca como revisado o descartado.

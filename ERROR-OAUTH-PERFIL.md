# Error de Perfil al iniciar con Google (OAuth)

## El Problema
Al iniciar sesin con Google (OAuth), el trigger de la base de datos (\handle_new_user\) falla o no se ejecuta porque Google no enva los metadatos esperados como \ccount_type\. 

Como resultado, el usuario queda registrado en autenticacin (\uth.users\), pero **no se crea su fila en la tabla \profiles\**.

Cuando el usuario intenta completar el formulario de bienvenida, el frontend ejecuta un \update\. Como la fila no existe, Supabase devuelve el error:
\Cannot coerce the result to a single JSON object\ (0 filas actualizadas).

## La Solucin
1. **Frontend:** Ya cambi localmente \update\ por \upsert\ en \profileService.ts\.
2. **Base de datos:** Falta agregar la poltica de INSERT en Supabase.

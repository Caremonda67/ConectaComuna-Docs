# Regla de Pruebas Locales

**Fecha:** Actualizado 15 sept 2026

## REGLA OBLIGATORIA ESTRICTA
Antes de hacer un commit o subir cdigo al repositorio remoto (GitHub), **SIEMPRE se debe probar de manera local.** 

**Motivo clave:** No llenar el historial del proyecto con commits basura o commits de arreglos en cadena. El historial de Git debe mantenerse limpio y profesional.

- Usar el servidor de desarrollo local (vite para frontend).
- Revisar que no haya errores de TypeScript o Lint en local.
- Confirmar visualmente o funcionalmente que el cambio hace lo esperado ANTES de empujar cambios (Push).
- Si una prueba falla en local, se arregla en local sin ensuciar el repositorio de GitHub.

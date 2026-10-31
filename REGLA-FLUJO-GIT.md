# REGLA OBLIGATORIA: FLUJO DE TRABAJO EN GIT Y RAMAS

Para mantener el orden del proyecto, evitar conflictos entre las 5 personas del equipo y simular un entorno de trabajo profesional (evaluado en la rúbrica), **queda estrictamente prohibido hacer commits directos o push a la rama `main`**.

## Políticas de Control de Versiones

1. **Siempre trabajar en una rama (Branching):**
   - Antes de iniciar cualquier código, esquema o fix, crear una rama nueva descriptiva.
   - Formato sugerido: `feat/nombre-funcionalidad`, `fix/nombre-error`, `chore/mantenimiento`.
   - Ejemplo: `git checkout -b feat/esquema-productos`

2. **No hacer Push sin Permiso:**
   - **NUNCA** hacer `git push origin main` de manera automática.
   - Siempre se debe pedir permiso y confirmación al usuario antes de hacer un push remoto de cualquier rama.

3. **Integración mediante Pull Requests (PR):**
   - El código se integra a `main` **únicamente** a través de Pull Requests.
   - Esto permite la revisión de código (Code Review) por parte de la coordinadora o compañeras antes de mezclarlo.

4. **Pruebas Locales antes del Commit:**
   - Como se indica en `REGLA-PRUEBAS-LOCALES.md`, todo el código debe ser probado en local (`tsc -b`, correr en localhost) antes de armar el commit.

**LEER ANTES DE PUBLICAR:** Si estás a punto de ejecutar `git commit` o `git push` estando en la rama `main`, **DETENTE**, crea una rama y pide autorización.

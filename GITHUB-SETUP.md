# AUTENTICACIÓN GITHUB — GUÍA

> Fecha: 1 sept 2026
> Guía para conectar los repos de Conecta Comuna con GitHub.
> **IMPORTANTE:** Nunca pegre tokens personales en archivos que se suban a git.

## Paso 1 — Reinicia la terminal

El comando `gh` se instaló pero el PATH de usuario se actualizó después de abrir la terminal. **Cerrar y volver a abrir la terminal/PowerShell** para que `gh` esté disponible.

Verifica que funciona:

```powershell
gh --version
```

Debe mostrar `gh version 2.98.0`.

## Paso 2 — Autentícate

```powershell
gh auth login
```

Responder a las preguntas:

1. **What account do you want to log into?** → `GitHub.com`
2. **What is your preferred protocol for Git operations?** → `HTTPS`
3. **Authenticate Git with your GitHub credentials?** → `Yes`
4. **How would you like to authenticate GitHub CLI?** → elegir **Login with a web browser**

Se abre el navegador. Inicia sesión en GitHub y anotar el **código de un solo uso** que debes escribir. Confirma autorizando.

> Alternativa (token): Si se prefiere un Personal Access Token (PAT), elegir **"Paste an authentication token"** al final.
> Crear el token en: GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
> Scopes necesarios: `repo`, `workflow`, `read:org`.

## Paso 3 — Verifica

```powershell
gh auth status
```

Debe mostrar la cuenta y permisos correctos.

## Paso 4 — Repos

Los 2 repos (backend y frontend) ya están creados:

- `ConectaComuna-Backend` → `backend/`
- `ConectaComuna-Frotend` → `frontend/`

## Nota sobre `npx`

En PowerShell está bloqueado `npx` por execution policy. Si algún paso de este proyecto lo exige, usar el equivalente directo con `node ".\node_modules\<pkg>\bin\..."`.

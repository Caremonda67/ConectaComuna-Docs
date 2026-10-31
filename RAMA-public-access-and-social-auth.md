# Registro de Rama: feat/public-access-and-social-auth

**Fecha:** 7 de septiembre de 2026
**Repositorio:** `ConectaComuna-Frontend`

## Descripción
Se ha creado una rama independiente para aislar los cambios relacionados con el acceso público y la mejora de la autenticación social, evitando afectar la rama de desarrollo principal (`feat/schema-facilitadores`) hasta que se valide la integración final.

## Contenido de la Rama
Esta rama contiene la implementación de:
- **Acceso Público al Directorio:** Modificación de la lógica de búsqueda para permitir el acceso sin sesión.
- **AuthGate (Muro de Registro):** Componente modal que intercepta las acciones de contacto (`WhatsApp`, `Llamar`, `Solicitar servicio`) para usuarios anónimos.
- **Identidad Visual Social:** Actualización de `SocialAuthButtons` con logotipos oficiales de Google y Facebook en formato SVG.
- **Corrección de Errores:** Solución al `ReferenceError` de inicialización de la variable `business` en la página de detalle.

## Archivos Modificados
- `src/pages/BusinessDetailPage.tsx`
- `src/components/auth/SocialAuthButtons.tsx`
- `src/components/auth/AuthGate.tsx`

**Estado:** Push realizado a `origin`. Pendiente de creación de Pull Request.

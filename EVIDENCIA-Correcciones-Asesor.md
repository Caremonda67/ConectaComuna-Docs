# Evidencia: Implementación de Retroalimentación del Asesor

**Fecha:** 7 de septiembre de 2026
**Sprint:** Sprint 0 / Inicio de Sprint 1

## Contexto
El asesor del proyecto nos dio retroalimentación sobre la versión que teníamos, enfocada en seguridad y usabilidad. De esa lista quedaron tres puntos en la versión entregada.

## Puntos Implementados

1. **Ubicación actual en crear cuenta:**
   - Integramos el hook `useNeighborhoodLocator` directamente en `RegisterPage.tsx`.
   - Se añadió un botón de GPS que permite detectar automáticamente el barrio durante el registro de la cuenta.

2. **Ubicación editable post-registro:**
   - Habilitamos que la ubicación sea dinámica desde el panel del usuario (`ClientDashboard.tsx`).
   - Se agregó lógica en `profileService.ts` (`updateNeighborhood`) para que el usuario pueda cambiar o corregir su barrio en cualquier momento, ya sea tecleándolo manualmente o usando el botón de detección por GPS.

3. **Código de apadrinamiento para facilitadores:**
   - Se eliminó el flujo inseguro donde cualquier facilitador podía enviar una solicitud desde el perfil público del negocio.
   - Ahora, el emprendedor tiene un campo `codigo_apadrinamiento` de 6 dígitos en su base de datos.
   - El dueño del negocio genera este código desde su `BusinessDashboard.tsx`.
   - El facilitador ingresa este PIN secreto en su `FacilitatorDashboard.tsx` para quedar vinculado de inmediato al negocio, asegurando que solo alguien de confianza y con el código pueda administrar el perfil.

## Impacto en la Rúbrica
- **Solución y resultados:** Atendemos directamente los hallazgos de seguridad y usabilidad señalados en la revisión.
- **Uso ético de TIC:** La identidad del negocio no se resuelve pidiendo más trámites al emprendedor, sino con el sello que otorga un facilitador del barrio y que queda registrado en la base con fecha y responsable.
- **Experiencia de usuario:** Facilitamos la actualización de datos (barrio) y simplificamos el proceso de apadrinamiento a un modelo de PIN numérico que la gente ya conoce (como los códigos de WhatsApp).

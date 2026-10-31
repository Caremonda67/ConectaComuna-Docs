# Auditor�a T�cnica del Frontend

**Fecha:** 21 de septiembre de 2026

Hicimos una revisi�n completa del c�digo fuente del frontend (revisi�n est�tica con TypeScript, Oxlint y an�lisis de flujo de servicios y componentes) antes de comenzar a trabajar en conjunto con el backend.

## 1. Hallazgos prioritarios (Bugs potenciales)

### A. Fallo de RLS al vincular con c�digo de 6 d�gitos
- **Archivo:** src/services/facilitadorService.ts (m�todo incularConCodigo).
- **Problema:** Intenta hacer insert en acilitadores_negocio con estado_vinculacion: 'aprobado'. La pol�tica de RLS en base de datos (acilitadores_insert_solicitud) solo permite insertar en estado 'pendiente'.
- **Impacto:** En modo demo funciona por ser local, pero contra la base de datos real en Supabase rebota con error de permisos (42501).

### B. Subida de fotos de facilitador a Storage
- **Archivo:** src/services/businessService.ts (m�todo uploadPhoto).
- **Problema:** Sube fotos en la ruta ${ownerId}/.... Si el facilitador es quien sube la foto, la carpeta no coincide con su propio uth.uid(), lo que puede bloquearse por la pol�tica de almacenamiento de Supabase.

### C. Advertencias y sincronizaci�n en el editor de negocio
- **Archivo:** src/pages/business/BusinessProfileEditor.tsx.
- **Problema:** Hay dos useEffect que compiten por asignar setManagedBusiness. Adem�s falta usiness en las dependencias del efecto principal. Si el negocio tarda unos milisegundos en cargar desde la red, el formulario puede quedar en blanco.

### D. Uso de tipo ny en captura de errores
- **Archivo:** src/pages/OnboardingPage.tsx (l�nea 52).
- **Problema:** Qued� un catch (e: any). Debe ajustarse a catch (e: unknown) para respetar la regla de cero ny en el proyecto.

## 2. Puntos de integraci�n y mejoras pendientes

- **M�dulo de Productos:** La tabla productos ya existe en la base de datos y en los tipos TypeScript, pero no hay pantalla ni formulario en el frontend para que el negocio liste sus productos con precio y foto.
- **Sincronizaci�n de esquemas:** El archivo ackend/schema.sql est� atrasado respecto al de rontend/supabase/schema.sql. Hay que igualarlos ahora que vamos a tocar ambos repositorios.

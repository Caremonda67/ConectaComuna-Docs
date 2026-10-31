# Módulo de Co-Administración (Facilitadores) — Plan de Implementación

**Fecha:** 6 sept 2026
**Asignado a:** Coordinación técnica (Luis Santiago)

## Contexto y requerimiento
El sistema debe permitir que un usuario (hijo, nieto, familiar) actúe como "Co-administrador" (Facilitador) de un negocio. 
El emprendedor (papá/abuelo) sigue siendo el dueño principal (owner), accede desde su celular y tiene control total (incluyendo eliminar el negocio). El co-administrador solo puede gestionar el perfil o pedidos, **después de que el emprendedor apruebe la vinculación**, y **no tiene permisos destructivos** (no puede borrar el negocio).

*Nota: Por directriz del Coordinador, todas las tablas nuevas se nombrarán en ESPAÑOL.*

## 1. Impacto en la Base de Datos (Schema)
Crearemos una tabla intermedia para manejar la relación de muchos-a-muchos entre perfiles y negocios, permitiendo el estado de "permiso pendiente".

### Modificación al Enum
- Añadir el rol al enum existente: ALTER TYPE account_type ADD VALUE 'facilitador';

### Nueva tabla: acilitadores_negocio
- id (uuid)
- 
egocio_id (uuid) -> referencia a usinesses.id
- acilitador_id (uuid) -> referencia a profiles.id
- estado_vinculacion (text) -> 'pendiente' | 'aprobado' | 'rechazado'
- creado_en (timestamptz)

### Actualización de Políticas de Seguridad (RLS)
El modelo de RLS actual solo permite al owner_id escribir en usinesses y orders.
- **Negocios:** Crearemos una política usinesses_facilitator_update que permita UPDATE (pero no DELETE) a los perfiles que estén en acilitadores_negocio con estado_vinculacion = 'aprobado' para ese 
egocio_id.
- **Eliminación protegida:** Se confirma que la política actual usinesses_owner_write sigue restringiendo el DELETE solo al owner_id.

## 2. Flujo Frontend
1. **Solicitud de acceso:** El Facilitador entra a la app, busca el negocio de su familiar y presiona "Solicitar acceso de administrador". Esto inserta un registro en acilitadores_negocio con estado pendiente.
2. **Aprobación del emprendedor:** El papá (dueño) entra a su panel, ve la notificación "Tu hijo Santiago quiere administrar tu negocio" y le da "Aprobar".
3. **Gestión:** El hijo ahora ve el negocio en su propio panel y puede editarlo (fotos, horarios, productos), pero si intenta borrarlo, la base de datos se lo bloquea.

## 3. Ramas de trabajo
- Backend/BD: eat/schema-facilitadores (modificaremos schema.sql).
- Frontend: eat/panel-facilitador (para crear las vistas).

## Pendientes / Preguntas
- Con este esquema definido, ¿procedemos a codificar el cambio en los archivos schema.sql (tanto del back como del front) en sus respectivas ramas para dejar lista la estructura de la base de datos?

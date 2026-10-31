# SCRUM — CONECTA COMUNA

**Calendario: 1 de septiembre al 13 de noviembre de 2026.** Cinco sprints de una semana para construir, y un tramo de cierre de seis semanas para pruebas, correcciones y la final en Bogotá.
SCRUM elegido: solo ~1 mes de desarrollo, necesita iteración rápida con el equipo y documentación de proceso (rúbrica: Metodología 15pts).

## Equipo y roles

| Persona | Rol SCRUM | Rol del sistema | Responsabilidades |
|---------|-----------|-----------------|-------------------|
| Luis Santiago | Product Owner | Facilitador | Prioriza el backlog con lo que salió de las entrevistas y coordina al equipo |
| Sergio | Scrum Master | Emprendedor/Cliente | Facilita, quita bloqueos y guarda las evidencias de cada sprint |
| Sebastián | Dev | Emprendedor/Cliente | Frontend: componentes, estados, accesibilidad y responsive |
| Luis Santiago | Dev | Facilitador | Backend: API, esquema, migraciones y pruebas |
| **Coordinación** | Dev y PO | Equipo (`equipo_conecta`) | Decisiones técnicas y revisión del trabajo del equipo |

## Sprints

| Sprint | Fechas | Objetivo | Meta (incremento entregable) |
|--------|--------|----------|------------------------------|
| 0 | 1-7 sept | Fundaciones + **Fase 0 entrevistas** | Repos+stack+DB schema listos; 5-10 entrevistas documentadas |
| 1 | 8-14 sept | Autenticación y perfiles | Registro/login funcional, perfil emprendedor creable (front+back) |
| 2 | 15-21 sept | Productos y directorio | CRUD productos + directorio público + precios visibles |
| 3 | 22-28 sept | Contacto, búsqueda y mapa | WhatsApp funcional + búsqueda/filtros + Leaflet |
| 4 | 29 sep-3 oct | Reseñas, rangos y paneles | Reseñas + rangos + panel emprendedor + **piloto y entregables** |

## Ceremonias (semanales, ~30-40 min)

- **Planificación** (lunes): qué entra al sprint
- **Review** (viernes): mostrar lo hecho con el equipo
- **Retrospectiva** (viernes): qué mejorar
- **Daily** (opcional, chat corto): qué hice / qué sigue / bloqueos

## Reglas del backlog

- Cada item = una tarea accionable con criterio de aceptación
- Prioridad: P0 (bloquea MVP) > P1 (importante) > P2 (deseable)
- Todo cambio de alcance pasa por el PO y se documenta en `docs/research/TABLA-DECISIONES.md`
- Al cerrar cada sprint: actualizar este archivo + agregar evidencia (capturas, commits, notas)

## Evidencias para la rúbrica

- `docs/scrum/sprint-N.md` — planificación + review de cada sprint
- `docs/entrevistas/` — fichas y fotos de entrevistas de campo
- `docs/research/` — investigación, hallazgos, decisiones
- `docs/design/` — wireframes, diario de diseño
- Git commits por feature (prueba de trabajo en equipo)

## Registro de avance

### 3 sept 2026 — Backend Fase 1 implementada
- Schema ampliado: `perfiles.auth_user_id` (1 usuario → N negocios), `lat`, `lng`, `verificacion_nivel` + índices.
- Rutas nuevas montadas en `/api`: categorias, comunas, perfiles, productos, solicitudes, resenas. Helper de auth (`src/config/auth.js`).
- El detalle de la API y las fases está en `docs/ARQUITECTURA-BACKEND.md` (Fases 2 y 3 pendientes).
- Probado: el servidor arranca y responde en `/api/salud`; las rutas quedaron montadas.

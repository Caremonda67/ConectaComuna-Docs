# RÚBRICA FEDESOFT — Conecta Comuna

> **Este es el documento MÁS importante del proyecto después del plan maestro.**
> Todo el trabajo (sprints, código, documentación, vídeo) debe poder explicarse contra esta rúbrica.
> Mantener ACTUALIZADO el estado de cada criterio. Es lo que necesitamos para ganar.
>
> Compilado desde: plan maestro (`Plan-Maestro-ConectaComuna.md` → sección "CÓMO CUBRE LA RÚBRICA")
> y reporte de entorno (Fedesoft × Redis × MinEducación, entrega **3 de octubre**, formulario + vídeo YouTube).
> **Verificar los puntos exactos contra las bases oficiales en fedesoft.org antes de la entrega.**

---

## Dato duro de la entrega (NO negociable)

- **Deadline: 3 de octubre de 2026** — entrega del proyecto TERMINADO vía **formulario oficial + vídeo de presentación en YouTube**.
- El entregable del 3 oct **no es solo el código**: es código + formulario + vídeo pulido (historia, problema real, demo, impacto).
- Alinear la narrativa con **ODS**: ODS 1 (fin de la pobreza), ODS 8 (trabajo decente), ODS 10 (reducir desigualdades), ODS 9 (industria/innovación), ODS 11 (ciudades sostenibles). Elegir 2-3 y enmarcar cada feature contra ellos.
- Equipo de 3-5 estudiantes del mismo colegio → documentar rolles/SCRUM/commits como evidencia de trabajo en equipo.
- Revisar que los 5 sprints cuadren contra el 3 oct (no 30 sep) y reserven tiempo para vídeo/documentación.

---

## TABLA DE RÚBRICA (10 criterios, 100 puntos estimados)

| # | Criterio | Pts | Descripción | ODS/evidencia clave |
|---|----------|-----|-------------|---------------------|
| 1 | Dominio conceptual y programación | 10 | Stack moderno, código organizado, arquitectura clara | React, Node, Supabase, SQL |
| 2 | Expresión oral | 5 | Explicar problema → solución → demostración | Ensayos |
| 3 | Organización presentación/vídeo | 5 | Vídeo demo 60-90 s, estructura clara | YouTube |
| 4 | Trabajo en equipo | 5 | Roles, rotación, commits como evidencia | Git/GitHub |
| 5 | Metodología | 15 | Investigación → entrevistas → necesidades → diseño → desarrollo → pruebas | **Fase 0** |
| 6 | Uso de herramientas | 15 | Git, GitHub, Supabase, Vercel, Render, Figma | Registro de uso |
| 7 | Diseño y calidad | 15 | Arquitectura limpia, responsive, mantenible | Tailwind, tokens |
| 8 | Experiencia de usuario | 10 | Interfaz simple, lenguaje sencillo, 3 pasos | Brief + referencia visual |
| 9 | Uso ético TIC | 5 | Licencias, imágenes autorizadas, privacidad | RLS, sin dirección exacta |
| 10 | Solución y resultados | 15 | MVP funcional, pruebas reales, métricas de impacto | Piloto |

> **Pendiente:** confirmar los puntos exactos de cada criterio en el documento oficial de Fedesoft
> (el plan maestro usa una estimación; hay que validarla con las bases del concurso).

---

## SEGUIMIENTO POR SPRINT

### Sprint 0 — Fundaciones + Fase 0 (1-7 sept)

| Criterio | Pts | Estado | Notas / Evidencia |
|----------|-----|--------|-------------------|
| 5 · Metodología | 15 | PENDIENTE | **Entrevistas de campo (5-10) — bloqueante. Las hace el usuario. Fichas + tabla de necesidades** |
| 6 · Herramientas | 15 | Parcial | Git/GitHub conectados, Vite+React+TS+Tailwind, Express+Supabase corriendo |
| 9 · Uso ético TIC | 5 | Parcial | RLS cerrado; imágenes reales pendientes |
| 1 · Programación | 10 | Parcial | Backend JS mínimalista, frontend 3 páginas placeholder |

**Fecha límite: 7 sept.**

---

### Entregas que suman (transversales)

- **Referencia visual** (`conecta-comuna-referencia`): sistema de diseño completo, **verificado en navegador**. → Alimenta criterios 7 y 8.
- **Tokens de diseño ya integrados** al frontend (`frontend/src/tokens.css` + Tailwind enlazado). → Criterio 7.
- **Wireframes low-fi** (10 pantallas MVP). → Criterio 7 (diseño/calidad) y 5 (proceso).
- **Reportes de investigación** (#1-#4). → Criterio 5 (metodología) y 10 (solución basada en evidencia).

---

### Sprints 1-4 (8 sept → 3 oct)

| Sprint | Objetivo | Criterios que destraban |
|--------|----------|--------------------------|
| 1 · Autenticación y Perfiles (8-14 sept) | Registro, login+JWT, perfil emprendimiento, alta asistida facilitador | 1, 10 |
| 2 · Productos y Directorio (15-21 sept) | CRUD productos, disponibilidad, upload, directorio, perfil público, precios | 1, 7, 10 |
| 3 · Contacto, Búsqueda y Mapa (22-28 sept) | WhatsApp, Leaflet, búsqueda+filtros, venta mayor | 8, 10 |
| 4 · Reseñas, Rangos y Paneles + ENTREGA (29 sept-3 oct) | Reseñas, rangos/insignias, panel emprendedor, **piloto (3-5)**, **vídeo demo, documentación, ensayos** | 2, 3, 4, 6, 10 |

---

## HUECOS CRÍTICOS (lo que más nos cuesta y NO tenemos)

1. **Fase 0 — Entrevistas (criterio 5, 15 pts)**: requisito oficial 5-10 entrevistas, ≥3 con baja experiencia digital, ≥1 que creció, ≥1 negocio establecido, de distintas actividades. **Bloqueante del criterio de más peso. Las hará el usuario.**
2. **Schema SQL**: el `schema.sql` actual (6 tablas) está **muy por debajo** del diagrama ER del plan maestro (faltan: `usuarios`, `facilitadores`, `denuncias`, `rangos`, `insignias`, `solicitudes` con cliente/emprendedor, `chat`, y columnas `verificado`, `vende_por_mayor`, `busca_distribuidor`, `lat/lng`).
3. **Código funcional**: no hay endpoints de negocio (perfiles, productos, reseñas, etc.) ni paneles (emprendedor/facilitador).
4. **Port de la referencia visual**: solo se portaron los **tokens**; faltan los componentes (`business_card`, `bottom_nav`, etc.).
5. **Entrega 3 oct**: sin vídeo demo, sin documentación de concurso, sin commits aún. Todo cae en Sprint 4 (riesgo alto de saturación).

---

## QUÉ HACER CADA VEZ QUE AVANCEMOS

1. Actualizar la columna **Estado** de los criterios que tocan este trabajo.
2. Guardar **evidencia** (capturas, commits, fichas de entrevista) en las carpetas correspondientes de `docs/`.
3. Preguntar siempre: *¿esto se puede defender frente a los jueces contra un criterio?*

---

## CHECKLIST FINAL ANTES DE LA ENTREGA (3 OCT)

- [ ] Entrevistas (5-10) + fichas + tabla de necesidades llenas
- [ ] MVP funcional con perfil, productos, búsqueda, contacto WhatsApp
- [ ] Piloto con 3-5 emprendedores reales + cambios aplicados/registrados
- [ ] Vídeo demo 60-90 s en YouTube (historia + demo + impacto)
- [ ] Presentación/ensayo de expresión oral
- [ ] Repos en GitHub con commits y evidencia de trabajo en equipo
- [ ] Documentación completa (diagramas, capturas, registro de cambios)
- [ ] Métricas de impacto (emprendedores, reseñas, contactos)
- [ ] Validar puntos exactos de la rúbrica contra fedesoft.org

---

*Documento maestro de rúbrica — Conecta Comuna. Mantener actualizado sprint a sprint.*

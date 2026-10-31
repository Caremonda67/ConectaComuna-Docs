# BACKLOG DE PRODUCTO — CONECTA COMUNA

Prioridad: **P0** MVP (imprescindible) · **P1** importante · **P2** deseable (post-MVP)
Estado: OK hecho · en curso · - pendiente

## Sprint 0 — Fundaciones + Fase 0 (1-7 sept)

| ID | Prioridad | Tarea | RF/HU | Estado |
|----|-----------|-------|-------|--------|
| B0-01 | P0 | Entrevistas de campo 5-10 emprendedores + fichas + tabla de necesidades | Fase 0 | OK (5 sept) — 4 fichas de campo documentadas |
| B0-02 | P0 | Repos GitHub conectados + estructura proyecto | — | OK (1 sept) |
| B0-03 | P0 | Wireframes low-fi v1 (10 pantallas MVP) | Diseño | OK (1 sept) |
| B0-04 | P0 | Setup backend: Express + Supabase + dotenv + health | — | OK (3 sept) |
| B0-05 | P0 | Setup frontend: Vite + React 19 + TS + Tailwind + router | — | OK (7 sept) |
| B0-06 | P0 | Schema Supabase: todas las tablas + RLS + storage | Todos | OK (7 sept) — tablas base, perfiles y políticas RLS |

## Sprint 1 — Autenticación y Perfiles (8-14 sept)

| ID | Prioridad | Tarea | RF/HU | Estado |
|----|-----------|-------|-------|--------|
| B1-01 | P0 | Registro de usuario con selección de rol (cliente, emprendedor, facilitador) | RF-13, HU-14 | OK (10 sept) — Supabase Auth con metadatos de rol |
| B1-02 | P0 | Login + JWT + middleware auth | RF-09, HU-11 | OK (11 sept) — sesión persistente y validación en API |
| B1-03 | P0 | Perfil de emprendimiento (crear/editar) | RF-01, HU-01 | OK (13 sept) — datos de contacto, categoría, barrio y fotos |
| B1-04 | P1 | Alta asistida por facilitador | RF-12, HU-12, HU-13 | OK (14 sept) — apadrinamiento con código PIN de 6 dígitos |
| B1-05 | P1 | Sello de verificación territorial por facilitador | RF-15 | OK (14 sept) — validación vecinal en territorio |

## Sprint 2 — Productos y Directorio (15-21 sept)

| ID | Prioridad | Tarea | RF/HU | Estado |
|----|-----------|-------|-------|--------|
| B2-01 | P0 | CRUD productos/servicios y catálogo de tarifas | RF-03, RF-04, HU-03, HU-04 | OK (17 sept) — tarifas fijas y cotizaciones desde |
| B2-02 | P0 | Gestión de disponibilidad (toggle inmediato) | RF-05, HU-05 | OK (18 sept) — control de abierto/cerrado en tiempo real |
| B2-03 | P0 | Upload de imágenes (Supabase Storage) | — | OK (20 sept) — fotos de negocios, productos y órdenes |
| B2-04 | P0 | Directorio público sin barrera de registro | RF-06, HU-06 | OK (7 sept / refinado 20 sept) |
| B2-05 | P0 | Perfil público del emprendimiento (`/negocio/:id`) | RF-02, HU-02, HU-07 | OK (21 sept) — catálogo, ubicación, fotos y contacto |
| B2-06 | P0 | Precios visibles y soporte de venta al por mayor | RF-17, RF-11 | OK (21 sept) — tarifas claras y precios por volumen |

## Sprint 3 — Contacto, Búsqueda y Mapa (22-28 sept)

| ID | Prioridad | Tarea | RF/HU | Estado |
|----|-----------|-------|-------|--------|
| B3-01 | P0 | Contacto por WhatsApp (mensaje predefinido con Trato Seguro) | RF-08, RF-09, HU-08, HU-09 | OK (23 sept) — plantilla estructurada sin intermediarios |
| B3-02 | P0 | Búsqueda y filtros (categoría, barrio, comuna, texto, mayorista) | RF-06 | OK (25 sept) — buscador con debounce y filtros combinables |
| B3-03 | P1 | Ubicación aproximada + mapa Leaflet/OSM (LazyMap) | RF-10, HU-10 | OK (26 sept) — cálculo de distancia y selector barrial |
| B3-04 | P1 | Venta al por mayor (badge en tarjeta + filtro en directorio) | RF-11, HU-11 | OK (28 sept) — compra mínima y precio especial |

## Sprint 4 — Reseñas, Rangos y Paneles (29 sept-3 oct)

| ID | Prioridad | Tarea | RF/HU | Estado |
|----|-----------|-------|-------|--------|
| B4-01 | P1 | Sistema de reseñas y calificación 1-5 estrellas | RF-16, HU-16 | OK (30 sept) — condicionado a pedidos completados |
| B4-02 | P1 | Rangos e insignias automáticos por trayectoria | RF-16, HU-17 | OK (1 oct) — cálculo por pedidos y reputación |
| B4-03 | P1 | Panel emprendedor: solicitudes, presupuesto y comprobante | RF-18, HU-18, HU-19, HU-20 | OK (2 oct) — cotización, anticipo y notas de avance |
| B4-04 | P1 | Panel admin/facilitador: moderación de reportes | RF-13, RF-14 | OK (2 oct) — consola de reportes comunitarios |
| B4-05 | P0 | Piloto, pruebas de flujo cliente-emprendedor y Modo Demo | Fase 3 | OK (3 oct) — respaldo local con demoBackend |
| B4-06 | P0 | Entregables: guion de video demo 90s, documentación y rúbrica | Rúbrica | OK (3 oct) — docs Frontend, Backend y General listos |

## Extensión — Ajustes Finales y Auditoría (4-5 oct)

| ID | Prioridad | Tarea | PR / Módulo | Estado |
|----|-----------|-------|-------------|--------|
| E-01 | P0 | Comprobante imprimible de Trato Seguro Comunal (máx. 50% anticipo, QR simulado) | Frontend PR #6 / Backend PR #5 | OK (4 oct) |
| E-02 | P1 | Modo oscuro cálido completo con persistencia en localStorage y contraste alto | Frontend PR #4, #5 y #12 | OK (5 oct) |
| E-03 | P1 | Badge en tiempo real de pedidos pendientes en barra de navegación y drawer | Frontend PR #9 | OK (5 oct) |
| E-04 | P1 | Consola interactiva de moderación comunitaria con resolución y descartado | Frontend PR #10 / Backend PR #8, #9 | OK (5 oct) |
| E-05 | P0 | Autorización delegada en Express para facilitadores vinculados y RLS estricto | Backend PR #10 | OK (5 oct) |

## Backlog técnico / mejoras post-concurso (P2)

| ID | Tarea | Nota |
|----|-------|------|
| C-01 | Login social (Google OAuth) | OK (7 sept, integrado con Supabase) |
| C-02 | Notificaciones push en navegador | En evaluación para segunda fase |
| C-03 | Expansión multi-comuna y selector de ciudad | Diseñado en schema, pendiente apertura geográfica |
| C-04 | App móvil nativa (PWA instalable / React Native) | Priorizada la web móvil accesible primero |
| C-05 | Pasarela de pagos en línea | Post-MVP; actualmente opera contra entrega con Trato Seguro |

## Registro histórico de hitos

### Cierre de Sprint 0 (7 sept 2026)
- Entrevistas presenciales completadas a 4 micro-emprendedores en territorio (Miles Andrés, Luis Jose, Ana Julieth, Alex David).
- Identificada la brecha digital en el 50% de encuestados: se formaliza el rol del Facilitador Comunitario.
- Configuración de repositorios separados, pipeline de Vite con React 19 y servidor Node.js Express.

### Cierre de Sprint 1 y 2 (21 sept 2026)
- Autenticación con Supabase Auth (correo + Google) y esquema relacional con perfiles, negocios y catálogo.
- Vinculación de apadrinamiento con código PIN de 6 dígitos para que el facilitador administre negocios de terceros.
- Subida de fotografías a Supabase Storage con control de políticas RLS.

### Cierre de Sprint 3 y 4 (3 oct 2026)
- Integración de Leaflet con OpenStreetMap en modo diferido (LazyMap) y cálculo de distancias por barrio.
- Canal directo a WhatsApp con plantilla formal de Trato Seguro Comunal.
- Sistema de pedidos con presupuesto estimado, fotos adjuntas y notas de avance.
- Reseñas verificadas exclusivas para pedidos completados y cálculo automático de insignias.
- Cobertura de contingencia para jurados: modo demo desacoplado (`demoBackend.ts`) con almacenamiento local.

### Ajustes de consolidación y cierre de ciclo (4-5 oct 2026)
- Emisión de comprobante oficial de Trato Seguro Comunal con límites al 50% de anticipo según Estatuto del Consumidor (Ley 1480).
- Consola de reportes comunitarios y moderación delegada en API Express y Supabase RLS.
- Auditoría de contraste para pantallas comunitarias y modo oscuro con persistencia.
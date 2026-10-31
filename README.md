# ConectaComuna — Documentación

Documentación del proyecto **ConectaComuna**, un directorio comunitario de
oficios y pequeños negocios de barrio, presentado a la final nacional de
Fedesoft 2026 (Bogotá, 13 de noviembre).

El código vive en otros dos repositorios:

- [ConectaComuna-Frontend](https://github.com/Caremonda67/ConectaComuna-Frontend) — React 19, TypeScript, Vite, Tailwind, Leaflet
- [ConectaComuna-Backend](https://github.com/Caremonda67/ConectaComuna-Backend) — Node.js y Express sobre Supabase

---

## Por dónde empezar

Si solo vas a leer tres documentos, que sean estos:

| | |
|---|---|
| [DOCUMENTACION-GENERAL](DOCUMENTACION-GENERAL.md) | Qué es el proyecto, la validación en campo, la metodología y cómo probarlo |
| [DOCUMENTACION-FRONTEND](DOCUMENTACION-FRONTEND.md) | Arquitectura de la aplicación, rutas, sistema de diseño y decisiones técnicas |
| [DOCUMENTACION-BACKEND](DOCUMENTACION-BACKEND.md) | API, esquema relacional, seguridad por fila en Supabase y decisiones técnicas |

## El resto, por temas

**Planificación y entrega**
[Plan maestro](Plan-Maestro-ConectaComuna.md) ·
[Rúbrica Fedesoft](RUBRICA-FEDESOFT.md) ·
[Plan de corrección hasta la final](PLAN-CORRECCION-2026-10-29.md) ·
[Plan para recuperar puntos de rúbrica](PLAN-PUNTOS-RUBRICA.md) ·
[Ensayo y contingencias](ENSAYO-Y-CONTINGENCIAS.md) ·
[Guion del video demo](GUION-VIDEO-DEMO.md)

**Arquitectura y análisis**
[Arquitectura del backend](ARQUITECTURA-BACKEND.md) ·
[Análisis técnico del codebase](ANALISIS-TECNICO-CODEBASE.md) ·
[Auditoría del frontend](AUDITORIA-FRONTEND.md) ·
[Para qué sirve cada tabla](Para-Que-Sirve-Cada-Tabla.md) ·
[Reconciliación de esquemas](RECONCILIACION-SCHEMAS.md) ·
[Estado real del frontend](AVANCE-FRONTEND-ESTADO.md) ·
[El Asistente Comunal con IA](ASISTENTE-COMUNAL-IA.md)

**Planes de módulo**
[Módulo de facilitador](PLAN-FACILITADOR.md) ·
[Búsqueda por ubicación](PLAN-UBICACION-NEGOCIOS.md)

**Normas de trabajo**
Las reglas que el equipo se dio y por qué:
[flujo de git](REGLA-FLUJO-GIT.md) ·
[buenas prácticas](REGLA-BUENAS-PRACTICAS.md) ·
[pruebas locales](REGLA-PRUEBAS-LOCALES.md) ·
[evidencias](REGLA-EVIDENCIAS.md) ·
[idioma de la base de datos](REGLA-IDIOMA-BD.md) ·
[orden de despliegue](REGLA-ORDEN-DESPLIEGUE.md)

La de orden de despliegue salió del día que el inicio de sesión se cayó una
hora: desplegamos el frontend antes que su migración. Está escrita para que no
se repita.

**Auditorías y evidencias**
[Auditoría de flujos antes de la final](AUDITORIA-FLUJOS-PREFINAL.md) ·
[Auditoría de sesión y seguridad](AUDITORIA-SESION-Y-SEGURIDAD.md) ·
[Evidencia de contraste WCAG](EVIDENCIA-CONTRASTE-WCAG.md) ·
[Huecos argumentativos](HUECOS-ARGUMENTATIVOS.md) ·
[Evaluación del asistente](EVALUACION-ASISTENTE-IA.md) ·
[Correcciones del asesor](EVIDENCIA-Correcciones-Asesor.md) ·
[Error de perfil con OAuth](ERROR-OAUTH-PERFIL.md) ·
[Rama de acceso público y login social](RAMA-public-access-and-social-auth.md) ·
y las demás `EVIDENCIA-*.md`, también en [`scrum/`](scrum/)

**Investigación de campo** → [`investigacion/`](investigacion/) y la
[ficha de entrevista](entrevistas/FICHA-ENTREVISTA.md) usada en la Fase 0.

**Proceso SCRUM** → [`scrum/`](scrum/)

**Diseño** → [`design/`](design/)

---

## Sobre la honestidad de estos documentos

Varios de estos textos se corrigen a sí mismos: dicen qué se midió mal la
primera vez y por qué. Por ejemplo,
[EVIDENCIA-CONTRASTE-WCAG](EVIDENCIA-CONTRASTE-WCAG.md) recoge tres formas de
medir el contraste que daban falsos positivos, y
[PLAN-PUNTOS-RUBRICA](PLAN-PUNTOS-RUBRICA.md) conserva el plan original con una
nota arriba de lo que resultó falso.

Está hecho a propósito. Un documento que solo cuenta los aciertos no sirve para
decidir nada, y en una sustentación se nota.

## Qué no está aquí

Tres documentos de trabajo quedan fuera a propósito, y el `.gitignore` dice
cuáles: los apuntes internos de defensa y dos catálogos de herramientas del
entorno local de un integrante. No son documentación del proyecto.

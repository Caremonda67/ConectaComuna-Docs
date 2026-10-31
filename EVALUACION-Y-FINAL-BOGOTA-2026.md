# Clasificación a la Gran Final Nacional Fedesoft 2026 — Estrategia y Evaluación

**Fecha:** 25 de octubre de 2026  
**Evento:** X Concurso Nacional de Programación (Fedesoft / REDIS / MinEducación)  
**Fecha de la Gran Final:** Viernes 13 de noviembre de 2026 (7:30 a.m. a 1:00 p.m.)  
**Lugar:** Bogotá D.C., Colombia (Presencial)  
**Puntaje de Clasificación Semifinal:** **92.20 / 100**  
**Conformación del equipo actual:** **3 personas (2 Backend + 1 Frontend)**  
**Fuente de la nota:** correo de notificación de Fedesoft y REDIS, revisado por el equipo el 25 de octubre. La transcripción está en la sección 1 y el mensaje original se guarda en `docs/evidencias/` para mostrarlo si un jurado lo pide.  

---

## 1. Notificación Oficial de Selección

> "El comité organizador del X Concurso Nacional de Programación le notifica que su proyecto fue seleccionado como finalista del X Concurso Nacional de Programación. La gran final será de forma presencial en Bogotá el viernes 13 de noviembre, de 7:30 a.m. a 1:00 p.m. En la final utilizaremos la misma rúbrica para la evaluación de los proyectos; los jurados serán más exigentes en los aspectos técnicos."  
> — *Diana Porto (Fedesoft) & Francisco José Correa Zabala (REDIS)*

### Parámetros de la Final Presencial
- **Tiempo de presentación (Pitch + Demo):** 10 minutos cronometrados.
- **Ronda de preguntas del jurado técnico:** 5 minutos intensivos.
- **Exigencia técnica:** Nivel superior en arquitectura, seguridad, concurrencia y validación de impacto.

---

## 2. Radiografía de Calificaciones (Rúbrica Oficial Fedesoft)

| Criterio | Puntaje Obtenido | Puntaje Máximo | Diagnóstico y Oportunidad |
| :--- | :---: | :---: | :--- |
| **Dominio del tema** | 9.0 | 10.0 | Fuerte. En vivo debemos demostrar dominio total sin dudar. |
| **Expresión oral** | 4.6 | 5.0 | Alto. El evaluador pidió evitar lectura; debe ser 100% conversacional. |
| **Organización del vídeo / presentación** | 4.8 | 5.0 | Impecable. Mantener la estructura clara en los 10 minutos. |
| **Trabajo en equipo** | **5.0** | **5.0** | **Puntaje perfecto.** Refleja el orden en Git, PRs y roles definidos. |
| **Metodología (Scrum)** | 13.6 | 15.0 | Espacio de mejora (+1.4 pts): mostrar sprints, retrospectivas y métricas. |
| **Uso de herramientas y recursos** | 14.2 | 15.0 | Casi perfecto (+0.8 pts): destacar React 19, Supabase RLS, Express y Leaflet. |
| **Diseño, presentación y calidad estética** | 13.4 | 15.0 | Espacio de mejora (+1.6 pts): modo oscuro accesible y microinteracciones. |
| **Experiencia de usuario (UX)** | 9.2 | 10.0 | Fuerte (+0.8 pts): resaltar facilidad para personas no letradas digitalmente. |
| **Uso ético de las TIC** | **5.0** | **5.0** | **Puntaje perfecto.** Estatuto del Consumidor, privacidad y Trato Seguro. |
| **Solución y resultados** | 13.4 | 15.0 | Espacio de mejora (+1.6 pts): métricas del piloto real y datos concretos. |
| **PROMEDIO GENERAL** | **92.20** | **100.0** | **CLASIFICADOS A LA FINAL** |

---

## 3. Desglose de Observaciones de los Evaluadores

### Evaluador 1
> *"El proyecto es interesante, y aporta directamente al territorio, es importante evitar la lectura en el desarrollo del video."*
* **Acción para Bogotá:** Para la tarima presencial queda prohibido leer diapositivas o notas en el teléfono. Cada uno de los 3 integrantes debe exponer su sección con naturalidad, ritmo conversacional y seguridad visual hacia el jurado.

### Evaluador 2
> *"Aunque es una herramienta muy normal, un marketplace, el objetivo social es importante. Hay temas por revisar, como el referente a cómo lograr una penetración exitosa en el mercado. También se le puede incluir un asistente de IA que reemplace al facilitador en algunos escenarios."*
* **Diferenciación vs Marketplace:** No somos un marketplace comercial tipo MercadoLibre o Rappi. Somos un portal comunal de oficios informales con Trato Seguro barrial, mediación vecinal, contacto directo por WhatsApp y modelo de facilitador para adultos mayores.
* **Penetración de mercado comunal:** Presentar el plan de alianzas barriales: Juntas de Acción Comunal (JAC), líderes territoriales, volanteo en puntos clave (panaderías, peluquerías) y red de confianza boca a boca.
* **El Asistente de IA (Feature Clave):** El jurado dio la clave de la victoria. Diseñar e implementar el asistente de dictado por voz para que el emprendedor que no sabe escribir pueda simplemente hablar y la IA estructure su servicio y precios automáticamente.

### Evaluador 4
> *"Se resalta la calidad en la producción visual y el uso de todas las herramientas descritas para la rúbrica."*
* **Acción para Bogotá:** Respaldar la calidad visual con la arquitectura técnica robusta (esquema relacional en PostgreSQL, políticas RLS, API REST en Express y sistema de diseño sin plantillas prefabricadas).

---

## 4. Benchmarking Competitivo — Lecciones de la Final 2025 (Edición IX)

Análisis de la transmisión oficial (`NwCtxWp-Vjk`, Universidad Libre de Bogotá):

1. **1er Lugar — EcoScan (Aguachica, Cesar):**
   - Ganaron porque llevaron una demo en vivo de Inteligencia Artificial práctica (escaneo y clasificación de residuos reciclables en tiempo real) ligada a recompensas locales.
   - Demostraron que la IA aplicada a un problema del territorio local enamora a los jurados de Fedesoft.
2. **2do Lugar — TeCuidApp (Colegio Los Nogales, Bogotá):**
   - Aplicación de prevención de violencia de género con mapas interactivos de zonas seguras y red comunitaria de auxilio.
   - Enfoque social anclado a la comunidad y georreferenciación clara.
3. **Puntos comunes de los proyectos ganadores:**
   - 0% lectura en la presentación.
   - 60% del tiempo dedicado a la demostración en vivo (software real en funcionamiento).
   - Respuestas técnicas directas y fundamentadas en seguridad y escalabilidad durante los 5 minutos de preguntas.

---

## 5. Estructura del Equipo (3 Personas)

El equipo queda en **3 integrantes**:
- **Backend (2 integrantes):**
  - **Luis Santiago:** Coordinación general, arquitectura técnica, verificación de estándares y desarrollo backend (Node.js, Express, PostgreSQL / Supabase, RLS, seguridad).
  - **Sergio:** Desarrollo de endpoints, pruebas de integración de API, lógica de negocio y persistencia.
- **Frontend (1 integrante):**
  - **Sebastián:** Interfaz y experiencia de usuario (React 19 + TypeScript + Vite + Tailwind CSS), mapas Leaflet, accesibilidad y componentes interactivos.

### Asignación de Roles para la Presentación de 10 Minutos en Bogotá
1. **Apertura y Problema Territorial (2 min):** Luis Santiago. Contexto barrial de la Comuna en Cali, testimonios de oficios informales y la barrera digital.
2. **Demo en Vivo del Producto (4 min):** Sebastián. Flujo de cliente buscando por mapa/cercanía, Trato Seguro, y la función estrella del Asistente de IA para el emprendedor no digitalizado.
3. **Arquitectura Técnica y Seguridad (2 min):** Sergio. Explicación del stack fullstack, base de datos relacional, políticas de seguridad RLS en Supabase y API Express.
4. **Impacto, Penetración Comunal y Cierre (2 min):** Luis Santiago. Modelo de facilitador, alianza con JACs y sostenibilidad comunitaria.
5. **Ronda de Preguntas (5 min):** Reparto coordinado según el área técnica consultada (Luis Santiago y Sergio responden backend/seguridad; Sebastián responde UX, accesibilidad y rendimiento en clientes móviles).

---

## 6. Plan de Trabajo Rumbo al 13 de Noviembre

1. **Sprint Final — Feature Asistente de IA:**
   - [x] **Completado (25 oct 2026):** Módulo de dictado por voz (Web Speech API) y lectura automática (TTS), integración con ubicación barrial activa y motor híbrido Gemini + respaldo local en backend y frontend. Ver [[ASISTENTE-COMUNAL-IA]].
2. **Consolidación de Métricas de Campo:**
   - Documentar la validación con negocios reales de Cali (Doña Marta, Suanfer Barber Studio, Don Fabio) con fotos y métricas de uso.
3. **Ensayos Intensivos de Pitch:**
   - Ensayar el guion de 10 minutos con cronómetro estricto.
   - Simular preguntas difíciles del jurado sobre seguridad, concurrencia, costos en la nube y penetración de mercado.

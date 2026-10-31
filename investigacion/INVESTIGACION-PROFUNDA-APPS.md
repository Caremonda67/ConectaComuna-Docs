# INVESTIGACIÓN PROFUNDA — PSICOLOGÍA, FACILIDAD DE USO Y PATRONES A IMPLEMENTAR
## Conecta Comuna · Parte 2 (guía de diseño accionable)

> **Fecha:** 1 sept 2026 · **Depende de / complementa:** `INVESTIGACION-UX-RESEARCH.md`
> **Propósito:** profundizar EN QUÉ falla cognitivamente el adulto poco digital, la psicología detrás de las decisiones (choice architecture, pérdida, hábitos) y PATRONES CONCRETOS a robar de apps exitosas (Airbnb, Etsy, Duolingo, WhatsApp, marketplaces). Cada recomendación indica código/UI o decisión de producto.
> **Documento interno — NO subir a GitHub.**

---

## PARTE A — LA PSICOLOGÍA DETRÁS DE LA APP (capa profunda)

### A.1 El cerebro decide con "Sistema 1", no con razones (Kahneman)

Los usuarios no evalúan tu app con lógica; la evalúan con **fluidez cognitiva**: si algo se procesa rápido, se siente verdadero y seguro; si cuesta, se siente sospechoso. Para el adulto poco digital, cuya capacidad de "Sistema 2" (deliberado) ya está mermada por esfuerzo extra de aprendizaje, **casi todo lo que haces se decide por Sistema 1**.

**Lo que ROBAR de aquí:** cada pantalla debe reducir micro-fricciones (menos texto, menos pasos, una sola pregunta) para que el Sistema 1 diga "esto es fácil = esto es bueno". No es decoración: es el motor de la confianza.

### A.2 Choice Architecture (Thaler & Sunstein) — con cifras

Cómo se presentan las opciones decide más que las opciones mismas. Evidencia (experimentos ~5968 usuarios + campo 594,997, y meta-análisis Nature Hum. Beh. sobre nudges):

1. **Integración en una sola elección** → integra varias decisiones en un solo paso sube adopción (disminuye "ruido de decisión" y atención a features individuales).
2. **Color según hábito** (apariencia como el patrón habitual de WhatsApp/mercados) acelera decisiones. → **Los botones de WhatsApp deben verse exactamente como WhatsApp.**
3. **"Defaultless defaults"** → redactar opciones como si activar la feature fuera la respuesta por defecto ("Continuar →") sube adopción sin forzar.
4. **Defaults éticos:** pre-selecciona lo que el usuario elegiría con información completa. Pero nunca defaults que solo beneficien a la plataforma — el usuario lo descubre y pierdes confianza de forma **irreversible** (función escalón, no lineal).

**Aplicación Conecta Comuna:**
- **Alta de negocio en PASOS integrados** (paso 1 = negocio+foto, paso 2 = WhatsApp+comuna, paso 3 = "publicar"). No 10 campos sueltos.
- **Disponibilidad por defecto "hoy disponible"** y **"visible en el directorio"** pre-marcado como sí (respecta lo que el emprendedor quiere: ser visto).
- **Botón WhatsApp** con el verde y el icono estándar de WhatsApp → aprovecha el esquema aprendido.

### A.3 Aversión a la pérdida — la ley más importante para tu contexto

Las pérdidas duelen ~2× más que las ganancias equivaless. Y aquí hay cifras de un campo real con 14M usuarios (Product Philosophy 2025) que cambian cómo debes diseñar:

- **El ratio de aversión a la pérdida NO es constante (2.25):** depende del usuario y del contexto.
- **Para ALIMENTOS/experiencia/servicios de confianza** el ratio sube a **3.8-5.0** (¡es tu categoría: comida y servicios del barrio!). La gente teme mucho perder en comida/servicios → la confianza es crítica.
- **Los usuarios NUEVOS NO deben ver frames de pérdida.** El loss-framing a usuarios nuevos redujo conversión 6-11%. Para alguien sin historial, el mensaje correcto es de **ganancia** ("mira lo que tu barrio tiene"), NO de urgencia ("se te acaba").
- **Reseñas negativas pesan ~3× más que las positivas:** cada review de 1★ baja conversión 3.2pp; cada 5★ la sube solo 1.1pp. → **Proteger el umbral** (no mostrar promedios con pocas reseñas) es una decisión de producto, no cosmética.
- **Umbral 44px → 31% error de toque; 56px → 4%.** En comida/edad esto es literalmente tu audiencia.

**Aplicación:** para el CLIENTE (nuevo, baja alfabetización) usa **frames de ganancia y calma**, nunca urgencia ficticia. Para el EMPRENDEDOR (que invierte y acumula reputación) la aversión a la pérdida es altísima → el dashboard debe mostrar SU progreso (su reputación, sus reseñas) porque teme perderlo.

### A.4 Hook Model (Nir Eyal) — retención, adaptado con cuidado

Los productos adictivos pasan por: **Disparador → Acción → Recompensa variable → Inversión**. Datos duros: las apps pierden 70-80% de usuarios a los 30 días; el día 7 predice el hábito.

Para Conecta Comuna, el "hook" debe adaptarse al público (éticamente y por capacidad):

| Fase | Aplicación Conecta Comuna | Consideración ética / adulto |
|---|---|---|
| **Disparador** | Notificación/recordatorio: "pan de Doña Marta se publicó hoy", "3 negocios nuevos en tu comuna" | Los adultos NO quieren spam; usar 1 disparador relevante/día como máximo, claro y con beneficio. La investigación (telehealth senior) dice: **repetición y consistencia** bajan la carga, no la suben. |
| **Acción (fácil)** | Contactar por WhatsApp con mensaje precargado = acción de 1 toque | Es EL flujo más fácil posible: cero formularios. |
| **Recompensa variable** | "Descubrir" algo nuevo cada visita: negocios nuevos, platos del día, destacados distintos | Recompensa de "hunt" (información). Juicioso: novelty activa dopamina, pero para adultos mayores el foco debe ser utilidad, no enganche adictivo. |
| **Inversión** | El emprendedor sube foto/producto → el negocio mejora con uso; el cliente guarda favoritos/comuna | **La inversión compone valor y crea hábito.** Esto ya está bien: cuanto más completa el emprendedor su perfil, más visible y valioso es. Duolingo: el "streak" (racha) es valor acumulado que la gente no quiere perder. Para el emprendedor, su "streak" = su reputación y reseñas. |

**Regla de oro ética (Eyal, Manipulation Matrix):** solo construye hábitos que MEJORAN la vida del usuario y que tú mismo usarías. Un directorio que ayuda a un tendero a ser encontrado es un hábito que beneficia — NO es manipulación. Evita: urgencia falsa, notificaciones manipulativas, gamificación que explote al adulto.

---

## PARTE B — LA FACILIDAD DE USO DEL USUARIO (capa profunda)

### B.1 Progressive Disclosure: la técnica con efecto más medido (+datos brutales)

Estudio real (MyWisdom, 2.847 sesiones, >60 años): el onboarding original con **8-12 decisiones simultáneas** → **74% de abandono**, porque excede la memoria de trabajo (4±1 ítems). Al rediseñar con **una pregunta por pantalla + indicador "Paso 3 de 7" + poder pausar/reanudar**:
- Onboarding completo: **26% → 84%**
- Reducción de carga cognitiva: **58%**
- Drop-off general: **-60%**

**Conclusión accionable: NINGÚN formulario de Conecta Comuna > 3 pasos con UNA pregunta principal por pantalla y barra de progreso.** Esto aplica a: tema registro de emprendedor, publicar producto, y el alta asistida por el facilitador.

### B.2 Touch targets y ergonomía (cifras exactas)

- Tamaño táctil: **36px → 31% errores**; **56px → 4% errores** (con confirmación háptica). La guía WCAG/Android pide ≥44px; la recomendación fuerte para adultos es **48-56px**.
- **Espaciado ≥4mm entre botones** (evita toques accidentales, confirma estudio UNAD de botones juntos).
- **Feedback multimodal** (visual + sonido + leve vibración) ↓ abandono 42% y acelera la recuperación de errores.
- **Un solo CTA primario por pantalla** (está en tu wireframe W-01 correctamente).

### B.3 Navegación: el hallazgo CONTRARIO a la intuición ("performance-affect paradox")

Estudio 2026 (Frontiers): simplificar al extremo **NO** es siempre lo mejor para adultos. El resultado:
- Alta complejidad de navegación → ↑ carga cognitiva y ↓ emoción positiva.
- **"Diversidad moderada"** (multi-modo: texto + icono + sonido, con categorías claras) → ↑ emoción positiva y autoeficacia **sin** sobrecargar.
- Un subgrupo de "**aprendices cautelosos**" tardaron más pero reportaron **más control y afecto positivo**: para ellos el tiempo extra es señal de confianza, no de fracaso.

**Implicación:** no reduzcas la navegación a un lienzo vacío y minimalista que prive al usuario de sentir que "lo está haciendo bien". Diseña **navegación plana (≤2 niveles), bottom-nav estándar con texto+icono**, y permite que el usuario **explore por prueba y error** sin castigarle. El control percibido > la velocidad.

### B.4 La voz como aliado (y su peligro real)

Hallazgos (Frontiers, NASA-TLX, 40 participantes 60+):
- **La voz reduce la carga cognitiva** en novatos (hasta 24% menor que el touch), porque se alinea con la conversación natural.
- **PERO:** un error de reconocimiento de voz **derrumba la confianza** de golpe. La tolerancia a error y un "plan B" táctil de 1 toque importan más que la precisión.

**Aplicación práctica:** no hagas del registro con voz una puerta obligatoria en el MVP (riesgo de confianza). En su lugar: (a) mensajes **leídos en voz alta** (texto-a-voz) al pulsar, (b) si implementas voz, siempre con fallback táctil claro. La voz es PRIORIDAD 2 complementaria.

### B.5 Onboarding: "¿qué me ayuda a hacer?" NO "features"

Principio (senior onboarding converto.pro, telehealth):
- Primera pantalla responde **"¿Qué me ayuda a hacer esto?"** (resultado), no "mira todas las funciones, permisos, o crea cuenta".
- Guiar al resultado: "Encuentra el pan de tu barrio en 1 toque" → luego revelar lo necesario.
- **Tutoriales largos son contraproducentes.** Mejor coach marks puntuales, u omitibles sin castigo.
- **Asistencia humans** (el facilitador) como camino explícito: rol "soy el dueño" vs "un facilitador me ayuda" (ya está en tu W-05 — validado).
- **Aprende la tarea en el momento** ("enseña el while a ejecutarla"), no en un tour previo.
- Entrada repetible siempre igual: SMS/QR/URL corta fija. Los adultos olvidan dónde empieza la tarea; la repetición baja la carga.

### B.6 Lenguaje: dignidad > sobre-simplificación

Hallazgo clave (patient UX 2026): simplificar en exceso puede sentirse **condescendiente** y baja la comprensión. "Respeto con precisión + modo simple opcional" superó a "lenguaje banalizado" (+42% comprensión conservando dignidad).

**Aplicación:** usa lenguaje claro pero **no infantil**. "Inicia sesión" (no "Authenticate"), "Actualizar" (no "Sincronizar"). Frases completas y amables. Evitar jerga técnica SIEMPRE, pero también evitar tratar al adulto como incapaz (lo confirma la investigación de Treinta: no subestimarlos).

---

## PARTE C — QUÉ COPIAR DE OTRAS APPS (patrones probados)

### C.1 El "trust de 3 capas" (Airbnb, Etsy, Vinted) — tu sistema completo

Los marketplaces que escalan construyen TRES capas de confianza simultáneamente. La mayoría solo construye la del medio. Conecta Comuna debe construir las tres, adaptadas:

| Capa | Pregunta que responde | Señal en Conecta Comuna |
|---|---|---|
| **Identidad** (¿es real?) | "¿Esta persona existe?" | **Badge "Verificado ✔"** por el facilitador (que visita el negocio). Verificación ESPECÍFICA: "negocio verificado en persona", no genérica. |
| **Reputación** (¿es confiable?) | "¿Cumple lo que promete?" | Reseñas de clientes con nombre + umbral de seguridad + respuesta del emprendedor. |
| **Comunidad** (¿es como yo?) | "¿Es de mi barrio?" | Sede/comuna visible, "tu barrio", fotos del local real, lenguaje local. |

**Verificación específica (del manual de trust pages):** en vez de un check genérico "verificado", di exactamente qué se chequeó: "Visita en persona por el facilitador" o "Documento verificado". La especificidad genera confianza; la genérica genera escepticismo.

**Primeros usuarios verificados a mano (The Marketplace Guide):** verifica manualmente los primeros 20-50 negocios. Cada facilitador = una verificación humana en terreno. Eso es un trust signal que ningún algoritmo reemplaza y que ETSY/AIRBNB usaron al inicio.

### C.2 Diseño de reseñas — aplicar el umbral y la protección (los datos mandan)

De los datos (Product Philosophy + TrustSync + Shopappy):
- Las reseñas **verificadas de compra** suben la conversión ~15%; 5+ reseñas duplican la conversión vs 0.
- **El promedio con <3-4 reseñas NO debe mostrarse** (ruido + efecto "5.0 sospechoso").
- Las reseñas negativas pesan 3×: protege el mínimo o un mal promedio hunde al emprendedor nuevo Y mina la plataforma.
- **Importante para WhatsApp-first:** si el pedido termina en WhatsApp (no dentro de la app), ¿cómo verificas la compra para la reseña? Opciones: (a) el emprendedor marca "pedido entregado", (b) el cliente confirma. Esto convierte una reseña en "verificada" → **capa de identidad + reputación a la vez.**

**Recomendación técnica:** en `resenas`, un campo `compra_verificada` (bool) y `noches/respuestas`. Mostrar solo promedios con N≥4. Badge "Compra verificada" en reseñas reales. Esto es exactamente lo que hace Etsy.

### C.3 Aprendizajes de WhatsApp (el canal que el adulto YA domina)

- **Patrón dominante:** el adulto opera por conversación, no por menús. Que el flujo de "pedir" termine en WhatsApp ES la jugada maestra.
- **Mensaje precargado** con contexto: "Hola {nombre del negocio}, vi tu {producto} por {precio} en Conecta Comuna, ¿lo tienes hoy? [Soy doña Marta, de la comuna 5]". Esto reduce la barrera de escribir y pre-califica al cliente. (¿`29` ya lo tienes en W-04.)
- **Consistencia de marca:** si el botón se ve como WhatsApp (verde, icono oficial), el esquema mental del adulto lo entiende al instante.
- **El facilitador como "contacto humano" de backup:** si el emprendedor no puede responder, el banner ofrece ayuda humana. (Ya en N4.)

### C.4 Aprendizaje de Treinta (1.4M usuarios micro-negocios)

- **Empieza donde el usuario está:** digitalizar la libreta, no imponer un sistema. El alta asistida por facilitador es tu "digitalizar la libreta por ellos".
- **Gratis y sin fricción.** (Conecta Comuna es gratis → correcto.)
- **Red social / efecto red:** cada negocio trae clientes. Fomenta que el cliente comparta el perfil del emprendedor por WhatsApp.
- **No subestimar:** crecer desde versión simple hasta avanzada a medida que el usuario se siente capaz. Tu rango → va en esa dirección.

### C.5 Gamificación/insignias — adaptada a adultos (recompensa de "self" y "tribe")

Duolingo prueba que el **streak** (racha/valor acumulado) retiene. Para Conecta Comuna, la gamificación NO debe ser infantil ni explotadora. Usa **recompensas de la tribu** (status visible: "Emprendedor del barrio ") y **del self** (maestría: "Completaste tu perfil 100%", "5 reseñas recibidas"). Que el progress bar de completar perfil ("tu perfil está 60% completo, añade una foto más para ser más visible") motive la inversión. Pero SIEMPRE honesto y no punitivo: gamificación que explota al adulto se nota y se rechaza.

### C.6 Precio transparente y "sin sorpresas" (Baymard / loss aversion)

- 48-70% de carritos se abandonan por **costos inesperados**. La gente odia que el precio cambie al final.
- **Tu wireframe W-04 ya muestra "Entrega en mi comuna +$2k" y el total** — esto es CORRECTO. Refuerza: mostrar SIEMPRE el costo total y cualquier extra ANTES de que el usuario contacte, en texto claro ("$7.000 total, $2.000 más si te la llevamos"). Este es un trust-builder enorme para el público desconfiado.
- **Nunca** agregar cargos sorpresa ocultos (es la peor violación de confianza y un dark pattern penalizable).

### C.7 Prueba social numérica del home (social proof honrado)

- "**X negocios de tu barrio ya están aquí**" o "**Y personas compraron local este mes**" cerca del CTA. Los números REALES y grandes reducen la incertidumbre del primer uso.
- Nunca fabricar números (ética + los adultos desconfiados lo detectan). Solo métricas reales.

### C.8 Seguridad percibida en el proceso (para desconfiados)

- Mensaje claro junto a cualquier dato que se pida: "**No te pedimos tarjeta ni datos bancarios** en esta app. El pago se hace directo al negocio." → neutraliza el miedo #1 identificado (fraude/dinero).
- Privacidad explícita: "Mostramos tu barrio, nunca tu dirección exacta." → ya lo tienes en RF-10, refuerza en UI.

---

## PARTE D — PRIORIDAD DE DISEÑO FINAL (ordena para el plazo y el jurado)

**IMPLEMENTAR SÍ O SÍ (núcleo — ataca confianza y facilidad):**
1. Alta de negocio/producto en **2-3 pasos con una pregunta por pantalla + barra de progreso** (progressive disclosure).
2. Botón **WhatsApp con mensaje precargado** + marka visual idéntica a WhatsApp. Contacto SIN login.
3. **Badge "Verificado" específico** ("negocio verificado en persona por el facilitador").
4. **Reseñas con umbral** (no mostrar promedio <4 reseñas) + badge "compra verificada" + respuestas del emprendedor.
5. **Toggle "letra grande / alto contraste"** + targets ≥48px (el rendimiento de accesibilidad más visible para el jurado).
6. **Mensajes de error/éxito humanos** + un solo CTA por pantalla.
7. **Precio 100% transparente** (total visible antes de contactar, sin cargos sorpresa).
8. **Prueba social numérica real** en el home y **frames de ganancia** para clientes nuevos.

**DESPUÉS (P1):** rangos combinados + insignias (self/tribe), accesibilidad de voz (texto-a-voz), facilitador avanzado, por mayor (RF-11).
**DESPUÉS (P2 / post):** mapa optimizado, chat interno, notificaciones push con límite ético (1/día relevante), multi-comuna, pagos en línea (cuando haya trust + métricas), app nativa.

**NO HACER:** urgencia falsa, scarcity fabricada, gamificación explotadora, pedir datos que no son imprescindibles para publicar, hacer del registro un formulario largo, o depender de gestos ocultos.

---

## FUENTES CLAVE (nuevas, además de las de la Parte 1)

- Kahneman, *Thinking Fast and Slow*; Thaler & Sunstein, *Nudge*; Cialdini, *Influence*
- Eyal & Hoover, *Hooked*; Hook Model (Trigger→Action→Variable Reward→Investment)
- Fogg Behavior Model (Motivation × Ability × Trigger)
- Nudge App Adoption (Journal of Marketing, 6 exp + campo 594,997)
- Loss Aversion Asymmetry in Digital Marketplaces (Product Philosophy, 14M users)
- Trust is asymmetric (Shopappy local marketplace / payments trust)
- Marketplace trust: identity/reputation/community layers (The Marketplace Guide; Otuny; GreenMoov; verify.top; onlinemarket.live)
- Review economics: verified reviews +15%, each 1★ -3.2pp (TrustSync / Product Philosophy)
- Progressive disclosure +abandonment: MyWisdom (26%→84%), Medizinich, Springer (74%→58%)
- Performance-affect paradox: Frontiers Psychology 2026 (navigation complexity vs diversity)
- Voice/cognitive load & trust: Frontiers Computer Science 2025 (NASA-TLX; error → trust drop)
- Touch targets 36/44/56px: UI design & cognitive performance (NASA-TLX); W3C WAI
- Onboarding senior: Converto, teleHealth UX, eSEOspace (outcome-first, plain language, dignity)
- Treinta (1.4M users), WhatsApp commerce (47% compras sociales Colombia)

---

*Documento de diseño profundo — Conecta Comuna — 1 sept 2026 — Interno (no subir a GitHub)*

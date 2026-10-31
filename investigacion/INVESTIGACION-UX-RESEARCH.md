# INVESTIGACIÓN EXHAUSTIVA UX — CONECTA COMUNA

> **Fecha:** 1 sept 2026 · **Estado:** v1 (base para mejorar el MVP)
> **Propósito:** entender qué hace a una app buena para adultos y personas con poca experiencia digital, comparar con competidores, y criticar/complementar la idea actual de Conecta Comuna.
> **Público objetivo:** emprendedores y clientes de barrio en **Cali** — personas adultas, en muchos casos sin formación tecnológica previa.
> **Fuentes:** ~25+ fuentes (W3C WAI, ACM SLR, JMIR, Springer, DANE/CCC/POLIS, La República, Portafolio, El Tiempo, NielsenIQ, Fenalco, Tiendanube, estudios de conversión).
> **Documento interno — NO subir a GitHub** (política de repos).

---

## RESUMEN EJECUTIVO

La investigación confirma 3 cosas que deben gobernar TODO el diseño de Conecta Comuna:

1. **El adulto mayor / poco digital en Colombia usa el celular casi exclusivamente para WhatsApp.** (DANE: 83% de adultos mayores tiene smartphone; 95% usa el celular como único acceso a internet. El 94% de colombianos con internet usa WhatsApp; WhatsApp = 47% de las compras por redes; 78% prefiere interactuar con marcas por mensajería.) **Conecta Comuna debe ser un "catálogo + disparador de WhatsApp", NO un e-commerce cerrado.**

2. **La barrera #1 para este público NO es la frustración técnica: es la CONFIANZA (miedo a la seguridad, fraude, pérdida de dinero).** La investigación colombiana (UPAEP, UNIMINUTO, UNAD, ECCI) es contundente: 67.4% cita desconfianza en seguridad digital como barrera principal; 58.2% no tiene formación; 40.4% prefiere tienda física. El diseño debe **reducir la ansiedad percibida**: reseñas verificadas, fotos reales, contacto claro, cero formularios de pago en línea en el MVP.

3. **"Simple" para este público NO es minimalismo estético: es reducir la carga cognitiva.** Letra grande (≥16px), botones ≥44×44px, un solo botón principal por pantalla, navegación plana/lineal, pocas opciones (Ley de Hick), cero gestos ocultos, mensajes de error claros. Todo esto tiene respaldo científico (W3C WAI, SLR 2025, Springer 2025).

**Idea central validada:** un directorio local básico YA existe (Directoriode.co, TiendasdeBarrio.CO, MiBarrio, Arequipa.app, Plaixe, VeciApp). Lo que hace ganar a Conecta Comuna es **el enfoque inclusivo y de confianza** para personas poco digitales + integración WhatsApp + el rol del facilitador (rostro humano). Pero hay riesgos de diseño que hay que corregir (detallados abajo).

---

# PARTE 1 — LA PSICOLOGÍA DE LO QUE HACE UNA APP BUENA PARA EL USUARIO

## 1.1 Cómo decide el cerebro (los fundamentos)

La investigación de conversión (2026) converge en unos pocos mecanismos neurológicos:

- **Fluidez cognitiva (cognitive fluency):** el cerebro confía en lo que procesa RÁPIDO. Una página que se entiende en segundos se siente "verdadera, segura y de calidad"; una que cuesta se siente "sospechosa", aunque sea legítima. (Zajonc; Build Grow Scale 2026.)
- **Carga cognitiva (cognitive load):** cuando la carga mental supera la capacidad, el cerebro NO se esfuerza más: **abandona** (bounce). El exceso de opciones, menús confusos, campos de más o navegación enrevesada es "carga extrínseca" que el diseño debe ELIMINAR. (Search Foundry 2026.)
- **Ley de Hick:** el tiempo de decisión crece logarítmicamente con el nº de opciones. Menos opciones = decisiones más rápidas Y más satisfacción (paradoja de la elección, Barry Schwartz).
- **Sesgo (heurística) de prueba social:** la gente usa el comportamiento ajeno como atajo de calidad. Es racional, no manipulable: funciona hasta en escépticos. **Prueba social puede subir la intención de compra hasta 270%** (Nielsen/BrightLocal).
- **Esquemas mentales (schemas):** el cerebro tiene una expectativa de "cómo se comporta una app de su tipo". Violar el esquema genera fricción y desconfianza; respetarlo genera fluidez. **Esto es clave para adultos: usar patrones que ya conocen (WhatsApp, listas simples, botones grandes).**
- **FOGG Behavior Model:** Comportamiento = Motivación + Habilidad + Estímulo. La mayoría solo optimiza el estímulo (el botón). Para este público, la HABILIDAD (facilidad para ejecutar) y la MOTIVACIÓN (confianza) son tan o más importantes.

**Aplicación para Conecta Comuna:**
- El flujo de descubrimiento debe poder completarse en **una sola pantalla de foco a la vez**, con UNA acción principal clara (p. ej. "Ver perfil" o "Contactar").
- Relaciones esquema: el botón de WhatsApp debe verse y comportarse como WhatsApp (icono verde, "Hablar por WhatsApp"), NO como un e-commerce con carrito.
- Reducir a lo esencial: un adulto no necesita 30 categorías; necesita ver las 4-6 categorías de su barrio primero.

## 1.2 La confianza como LA barrera principal (especial Cali/Colombia)

Evidencia colombiana (relevante porque Conecta Comuna es para Cali):

- **67.4%** de personas de 50-60 en Bogotá cita "desconfianza en seguridad digital" como barrera principal al e-commerce. (ECCI 2025.)
- **83%** de los adultos mayores que compran online lo hacen "esporádicamente o nunca". (ECCI 2025.)
- Estudio UPAEP 2025: adultos mayores en Bogotá reportan **altos riesgos percibidos** en compras online y presenciales; esto frena intención de compra y afecta indicadores de confianza (WOM/eWOM).
- Adultos mayores tienen **dificultad con registrar cuentas, contraseñas y formularios**; el login y registro son barreras citadas constantemente. (JMIR 2025, ECCI 2025.)

**Diseño de confianza (basado en modelo de confianza de McKnight y en LIFT):**

| Factor de riesgo | Lo que el usuario teme | Señal de confianza que lo mitiga |
|---|---|---|
| ¿Será un negocio real? | Perfil falso, estafa | Fotos reales del local, "verificado ✔" por el facilitador, reseñas con nombres |
| ¿Me estafarán (dinero)? | Pago en línea, fraude | MVP SIN pagos en línea; contacto por WhatsApp y pago en efectivo/contraentrega (como el comercio real de barrio) |
| ¿Mis datos están seguros? | Robo de datos | Qué solo se pide lo mínimo; aviso claro "no pedimos tarjeta" |
| ¿Podré confiar en la calidad? | Mala calidad, no cumplen | Rangos de confianza, reseñas, "compromiso de calidad" |

**Regla de oro:** los trust signals NO se apilan aditivamente (tres sellos seguros no valen 3×). Vale más **una señal por tipo de riesgo**, y lo más potente para compradores primerizos es: reseñas reales con nombres + foto del negocio real + diseño prolijo + canales de contacto claros. (Product Philosophy 2025.)

 **OJO importante:** las reseñas AMENAZAN a este público si el promedio es bajo. La investigación muestra que mostrar rating < 4.0 **reduce** la conversión. Y un 5.0 perfecto también genera sospecha. Estrategia: proteger el umbral mínimo (>4.0) y mostrar reseñas reales con detalle.

## 1.3 Accesibilidad y ergonomía para personas mayores (WCAG + edad)

Tanto W3C WAI como la literatura (ACM SLR 2025, Springer 2025, JMIR 2025) coinciden en qué funciona:

**VISIÓN**
- **Letra ≥16px** por defecto; permitir ajustar tamaño (Dynamic Type).
- Alto contraste (texto oscuro sobre fondo claro); **nunca** depender SOLO del color para transmitir significado (añadir texto/icono/label).
- Evitar azul bajo y tonos apagados; alto contraste de blanco y negro primero.

**MOTRICIDAD / TEMBLOR**
- **Áreas táctiles ≥44×44px** (recomendación de accesibilidad Android/Google).
- **Espaciar los botones** para evitar toques accidentales (muy relevante para artritis/temblores — UNAD lo confirmó: botones juntos = errores).
- Ofrecer **botones en lugar de gestos** (nada de "swipe" como única vía).

**COGNITIVO / MEMORIA**
- **Navegación plana y predecible** (evitar menús anidados profundos).
- **Pocas opciones** por pantalla (Ley de Hick).
- **Formularios divididos** (progressive disclosure): no pedir todo de golpe; pedir datos sensibles SOLO después de que el usuario se haya comprometido con los primeros pasos. (Fogg; ConvinceLab 2026 — este patrón subió conversión 34% en un caso real.)
- **Etiquetas claras en texto** junto a iconos (los iconos solos son ambiguos).
- **Evitar doble clic / doble envío** (muy común en este público) usando estados de desactivación y confirmación.
- **Mensajes de error y éxito claros** (lenguaje humano, no técnicos).

**VOZ (opcional pero potente)**
- Asistencia por voz / texto-a-voz es la recomendación de mayor impacto para baja alfabetización (JMIR, Springer).

**Aplicación Conecta Comuna:**
- Registro en 2 pasos como máximo; el alta de producto con pasos breves (no un formulario largo).
- Un solo CTA primario por pantalla, de alto contraste.
- Cero gestos ocultos; todo botón visible.
- Mensajes tipo "✓ Listo, tu producto está publicado" y errores tipo "Este campo falta: nombre del negocio" (no "Error 400").

---

# PARTE 2 — LO QUE ENSEÑAN LOS COMPETIDORES

## 2.1 El gran aprendizaje de Treinta (la app que SÍ conquistó al tendero colombiano)

Treinta es el caso de éxito más relevante: 1.4 millones de usuarios solo en Colombia, 6M en LatAm, Serie A de US$46M (la mayor de la historia de Colombia). Su modelo de adopción es EXACTAMENTE la población de Conecta Comuna: microempresarios poco digitales que "llevaban las cuentas en una libreta".

**Por qué funcionó (claves que copiar):**
1. **Empezó donde el usuario ya estaba:** digitalizó las libretas, no les impuso un sistema nuevo. "Una aplicación muy sencilla... enfocada en negocios que querían seguir manejando un cuaderno, pero de manera digital."
2. **Gratis y sin fricción inicial.**
3. **Reduce la carga de decisiones:** registrar venta "en segundos", por nombre o escaneando código de barras.
4. **Mobile + web + cada negocio va creciendo (versión simple → versión avanzada).** Diseñó para el punto de partida bajo, no para el ideal.
5. **Crecimiento orgánico: cada micro negocio trae clientes/proveedores.** La plataforma agrega valor de red.
6. **Interés comercial claro Y respeto al usuario:** añadió pagos, datáfono, marketplace B2B — pero SIEMPRE con la "experiencia de atención al cliente" como prioridad declarada.

**Lección para Conecta Comuna:** no esperes que el emprendedor "llene un formulario web". Diseña el alta tan simple como una conversación. El rol facilitador es el análogo de "digitalizar la libreta por ellos". **El MVP debe enfocarse en que el emprendedor SIENTA que pierde poco y gana visibilidad, sin aprender nada nuevo de contabilidad ni diseño.**

## 2.2 Directorios locales existentes (competencia directa)

| App | Qué hace | Fortaleza | Debilidad / lección |
|---|---|---|---|
| **Directoriode.co** | Directorio nacional de negocios con categorías, 19.508 negocios | Cobertura grande, categorías, contacto | Genérico, no inclusivo, no foco en confianza/acc. |
| **TiendasdeBarrio.CO** | Georreferencia comercios base de pirámide | Inclusión social, gratis | Baja adopción visible; lección: falta red social/confianza |
| **Mi Barrio App (mibarrio.co)** | Negocios locales cerca de ti | Local, categorías | Simple; poco diferencial |
| **VeciApp (Santa Marta)** | App social/comunitaria, conecta emprendedores vulnerables | Enfoque social + cooperación internacional | MUY parecido al nuestro; demuestra idea válida pero aún nicho |
| **Arequipa App Center** | Marketplace de emprendimientos | Registro fácil de negocio | Poca accesibilidad adulto; foco emprendedor, no confianza |
| **Plaixe (eje cafetero)** | Geolocalización + compra directa por WhatsApp + puntos fidelidad + panel | **Integra WhatsApp + catálogo + fidelidad** | Ejemplo de que el modelo "catálogo → WhatsApp" funciona; cobra ~300k COP a negocios |
| **ECO App** | Ecosistema local con perfil + catálogo + carrito + envío | Completo | **Carrito/pagos = fricción para nuestro público**; lección de lo que NO hacer en MVP |

**Síntesis competitiva:** el directorio no es novedad. Los que priorizan la **inclusión social** (VeciApp, TiendasdeBarrio) y la **integración WhatsApp** (Plaixe) van en la dirección correcta. **Conecta Comuna gana combinando: (1) enfoque confianza/inclusión, (2) WhatsApp-first, (3) rol facilitador cara a cara, (4) contexto real de barrio caleño (comuna).** Este combo no lo tiene ninguno.

## 2.3 Por qué WhatsApp es el rey del comercio local (no competencia, sino ALIADO)

Datos: WhatsApp = 47% de las compras sociales en Colombia; 84.1% de tiendas online lo usa para vender; 78% prefiere marcas por mensajería; 94% de colombianos con internet lo usa; 64% de tenderos lo usa. Fenalco: tienda de barrio = canal predilecto (35.3%).

**Psicología:** WhatsApp es el único canal que el adulto caleño YA domina. El miedo a "app de compras con tarjeta" desaparece si la transacción real termina en una conversación familiar de WhatsApp (inmediatez, familiaridad, persona de carne y hueso al otro lado).

**Implicación estratégica:** Conecta Comuna debe diseñarse como **"un catálogo digital que TERMINA en WhatsApp"**, no como un marketplace cerrado. El botón WhatsApp con mensaje precargado ("Hola, vi tu Pan tajado en Conecta Comuna, ¿tienes para 2?") es la función central. Esto elimina la barrera de confianza/pagos y de experiencia técnica.

---

# PARTE 3 — EL CONTEXTO DE CALI (datos duros)

- **2.285.099 habitantes (2025).** 44.4% del PIB del Valle.
- **268.000 micronegocios** en Cali; 42.6% liderados por mujeres (~114.000 iniciativas femeninas). 68.9% de esas mujeres aporta >50% del ingreso familiar.
- **61% de los emprendimientos de Cali están a cargo de mujeres**; 57% operan desde la casa (conciliar cuidado).
- **36% de los caleños viven en estratos 1 y 2; 53% de la población adulta de esos estratos no supera secundaria.**
- **~43% de la economía empresarial de Cali es informal** (22.192 unidades sin registro); en sectores específicos la informalidad llega a 41-48% (comuna 10).
- **Solo ~17% de los vendedores informales tienen aplicaciones bancarias digitales**; la mayoría opera en efectivo; 78.4% de los pagos del país son en efectivo.
- Negocios informales: 40% elaboran alimentos/panadería, 17% calzado, 13% confección, 10% madera/muebles. **60% de los dueños de negocios informales tienen 40+ años.**
- **54% de la población de Cali estudió máximo hasta secundaria** (POLIS). Baja alfabetización digital real.
- Economía de cuidado: 69.4% de las mujeres emprendedoras dedican 8+ h/día a labores no remuneradas → poco tiempo para aprender tecnología compleja.

**Todo esto refuerza:** usuarios con bajo capital humano digital + desconfianza + poco tiempo + uso casi exclusivo de WhatsApp + efectivo como medio de pago + necesidad de apoyo humano (facilitador). El diseño debe asumir el **nivel más bajo de alfabetización digital** como base, NO el promedio nacional "conectado".

---

# PARTE 4 — CRÍTICA Y COMPLEMENTO A LA IDEA ACTUAL DE CONECTA COMUNA

> Analizo el plan maestro + wireframes actuales y propongo refuerzos. Prioridad: mantener el alcance para el plazo (3 oct).

## 4.1 COSAS QUE YA ESTÁN MUY BIEN en el diseño actual (validar y conservar)

 **WhatsApp-first (RF-08, RF-09)** — el botón WhatsApp con mensaje precargado es LA decisión correcta. El MVP termina el pedido en WhatsApp (sin pagos). Refuerza la confianza.
 **Rol de Facilitador (RF-12)** — es el diferencial humano frente a competidores. CLAVE para baja alfabetización digital. Mantener y destacarlo en el video.
 **Cero pagos en línea en MVP** — correcto para este público (miedo, efectivo como norma).
 **Ubicación aproximada (RF-10)** — correcto (privacidad + confianza). No dirección exacta.
 **Lenguaje sencillo en perfil (W-03)** — refuerza inclusión.
 **Registro con "¿quién crea la cuenta?" dueño vs facilitador (W-05)** — buen modelo mental.
 **Rangos e insignias (RF-16)** — buena prueba social visible. (Perfeccionar: ver 4.4.)
 **Efectivo/"compra local" como mensaje** — conecta con lo que el cliente valora (Plaixe usa "apoya lo local").

## 4.2 CRÍTICAS / RIESGOS A CORREGIR

### C1 — El formulario de registro/alta es DEMASIADO largo para el público.
W-05 y W-08 piden muchos campos (nombre, categoría, descripción, comuna, dirección, teléfono, foto, y para el dueño: nombre, celular, contraseña). Para un adulto poco digital esto es carga cognitiva alta y abandono.

**Fix:** dividir en 2-3 pasos muy breves (progressive disclosure), pedir lo mínimo para PUBLICAR (nombre del negocio + categoría + WhatsApp + UNA foto + comuna). La descripción larga, precios mayoristas, horarios, etc. se agregan DESPUÉS de publicar (el perfil puede empezar básico y enriquecerse). El alta asistida por facilitador es la primera línea: que el facilitador llene los campos complejos, no el emprendedor.

### C2 — Muchas pantallas MVP para el emprendedor puede dispersar.
Cartel actual tiene 10 pantallas + flujos. Para el plazo, priorizar el flujo que da el "primer valor": **dar de alta + publicar 1 producto + verse en el directorio.** Si el emprendedor no ve su negocio "vivo y visible" en 5 minutos, se pierde.

### C3 — La cuenta "cliente" (login) puede ser barrera.
Para descubrir (ver directorio, ver perfil, OJALÁ contactar por WhatsApp) NO debe exigirse cuenta. La investigación muestra que login/registro es barrera #1 del adulto. El MVP debe permitir **contactar por WhatsApp SIN login** (solo el número público). Login solo para: emprendedor (crear/editar), facilitador, admin. Cliente anónimo verá todo y contactará; reseñas/favoritos SÍ requieren sesión (y va bien explicado).

### C4 — Reseñas y rangos: proteger el umbral de confianza.
- No mostrar una calificación agregada si hay < 3-4 reseñas (un promedio con 1 reseña genera desconfianza o el efecto "5.0 sospechoso").
- Mostrar reseñas con nombre y detalle (prueba social específica > genérica).
- El rango "Nuevo" ya es bueno para no castigar a quien empieza.

### C5 — Pendiente real: no confundir "funciones" (rangos, chat interno, mapa) con "lo que derriba la barrera de confianza".
El chat interno (RF-18/RF-20) y el mapa son "nice to have" que consumen días de desarrollo. La prioridad para el plazo y para el jurado es el **núcleo de confianza**: perfil real + fotos + reseñas verificadas + WhatsApp + facilitador. Recomiendo **relegar el chat interno y el mapa a P1/P2** si el tiempo aprieta, y que todo el contacto pase por WhatsApp en el MVP.

### C6 — Consistencia / esquema mental.
Si anuncias un directorio y luego el emprendedor se encuentra un "e-commerce", rompe el esquema. Mantener la metáfora de **"la vitrina digital de tu barrio"**: ver → contactar → comprar en persona/WhatsApp.

## 4.3 COMPLEMENTOS (mejoras basadas en investigación)

### N1 — "Verificado por Conecta Comuna" (badge de confianza)
Más potente que el rango por calificación para vencer el miedo al fraude. Cuando un facilitador o admin valida el negocio (idealmente visitándolo), se marca "✔ Verificado". Esto ataca directamente el riesgo "¿será real?" de McKnight. Mejor: badge de **single risk dimension** = institucional/realidad del negocio.

### N2 — Modo de texto grande / alto contraste (accesibilidad de 1 clic)
Añadir un toggle global "A A−" o "Letra grande" que aumente font-size y contraste. Barato de implementar (una clase CSS/estado React), gran impacto en percepción de inclusión (lo pide W3C, Springer, y los jueces de UX ven esto muy bien). Puede ser el "comodín" para la rúbrica de experiencia de usuario e inclusión.

### N3 — Mensajes de error y de éxito en lenguaje humano (no técnico)
Ej: error → "Este campo falta: nombre del negocio. Escríbelo aquí abajo." · éxito → " ¡Listo! Tu negocio ya está en el directorio." Recomendado por WCAG y por la literatura; muy visible para el jurado.

### N4 — "¿Necesitas ayuda? Un vecino te ayuda" (banner facilitador)
En el perfil del emprendedor y en el home, mostrar la opción de ser ayudado por un facilitador. Esto convierte la barrera (alfabetización digital) en un feature visible (inclusión). Refuerza el diferencial vs competidores.

### N5 — Prueba social numérica en el home
"X negocios de tu barrio ya están aquí" o "Y reservas/contactos este mes" cerca del CTA, para sembrar confianza del lado del cliente (Cialdini social proof). Siempre con números reales, no falsos (ética — evitar dark patterns, que son penalizados).

### N6 — Categorías del barrio real primero
Basado en datos de Cali (comida/panadería, calzado, confección, muebles, peluquería, miscelánea, droguería), pre-cargar las categorías que de verdad existen en los barrios, en orden de frecuencia real. Esto reduce la carga de elección del emprendedor (hoy W-08 pide "categoría ▾" con listas genéricas).

### N7 — Disponibilidad EXPLÍCITA ("¿Atiendes pedidos hoy?") en el perfil
La investigación de tiendas muestra que el stock agotado o "no disponible" es una de las peores experiencias (Treinta lo resalta). El toggle "visible/oculto" y "disponible/agotado" que ya tienes es correcto; asegurar que el cliente vea el estado ANTES de contactar (reduce fricción y aumenta confianza).

### N8 — Perfil editable por el emprendedor SIN técnico (auto-gestión simple)
Una vez dado de alta, que el emprendedor pueda cambiar foto/precio/disponibilidad él mismo con botones grandes. (Aunque el facilitador exista como apoyo, dar autonomía mejora la adopción y el orgullo de propiedad — requisito de la investigación: no tratar al adulto como incapaz.)

## 4.4 Ajustes finos al sistema de rangos (RF-16)

El sistema actual por "calificación promedio" es frágil para negocios nuevos (con 1-2 reseñas el promedio es ruidoso y puede dañar la confianza, ver C4). **Recomendación:** que el rango combine calificación promedio + Nº de reseñas, y que hasta tener 3-4 reseñas el perfil muestre "Nuevo" sin promedio expuesto. Las insignias por logros (primera venta, variedad, verificado, respuesta rápida) son excelentes y deben quedar visibles: son la prueba social más "humana".

## 4.5 Orden sugerido de PRIORIDADES (para el plazo restante)

**Núcleo imprescindible (MVP, en este orden):**
1. Directorio público + perfil (real, fotos, WhatsApp, verificado)
2. Alta de negocio (simple, 2-3 pasos) — con facilitador como apoyo
3. Alta/edición de productos con foto + precio + disponibilidad
4. Contacto WhatsApp con mensaje precargado (SIN login para el cliente)
5. Reseñas simples con umbral de seguridad (ver C4) + badge "verificado"
6. Modo letra grande + contrastes (accesibilidad de 1 clic)

**Después (P1, si hay tiempo):** rangos combinados, facilitador avanzado, por mayor (RF-11), panel admin de moderación, mapa.

**Después (P2 / post-MVP):** chat interno, notificaciones push, login social (Google), multi-comuna, app nativa, pagos en línea.

> Nota: el mapa (Leaflet) está en RF-10. Si supone rejas (Leaflet en React es fácil, pero el marcador de "negocio" con coordenadas requiere que el emprendedor conozca su ubicación geo), puede hacerse MUY simple: un marcador con la comuna o dirección aproximada ya escrita. Evaluar tiempo vs. valor para el plazo.

---

# PARTE 5 — RECOMENDACIONES FINALES ACCIONABLES (checklist para el equipo)

### UX / Inclusión (pesa en la rúbrica "Experiencia de usuario" y "Diseño")
- [ ] Letra por defecto ≥16px; toggle "letra grande + alto contraste" global.
- [ ] Botones ≥44×44px, espaciados.
- [ ] Una sola acción principal clara por pantalla.
- [ ] Navegación plana (máx. 2 niveles), consistente entre pantallas.
- [ ] Cero gestos ocultos; iconos SIEMPRE con texto.
- [ ] Mensajes de error/éxito humanos.
- [ ] Formularios en 2-3 pasos; pedir datos sensibles al final.
- [ ] Lenguaje sencillo en TODO (revisado por alguien no técnico).

### Confianza (pesa en "Solución y resultados" y adopción real)
- [ ] Contacto por WhatsApp sin login obligatorio.
- [ ] Badge "Verificado" (por el facilitador).
- [ ] Reseñas con nombre + umbral mínimo (no mostrar promedio <3-4 reseñas).
- [ ] Fotografías reales (obligatoria la primera; no usar stock genérico).
- [ ] Sin pagos en línea en el MVP (efectivo/contraentrega).
- [ ] Privacidad: ubicación aproximada (comuna/barrio), nunca dirección exacta salvo autorización.

### Adopción del emprendedor
- [ ] Alta en ≤3 pasos; publicar con 1 foto + categoría + WhatsApp + comuna.
- [ ] El facilitador es la primera línea para cargar datos complejos.
- [ ] Perfil editable por el emprendedor con botones grandes (autonomía).
- [ ] Vista previa "así te ven los clientes" para que vea el resultado.

### Estrategia / demo para el jurado
- [ ] Video 60-90s centrado en: ver directorio → ver perfil → contacto WhatsApp → rol del facilitador (rostro humano) → verificación.
- [ ] Mostrar el "antes/después" de un negocio sin internet → con perfil visible.
- [ ] Enfatizar el diferencial: inclusión + confianza + 100% local + gratis.
- [ ] Citar evidencia de campo durante el jurado (entrevistas Fase 0 + datos Cali del informe CCC/POLIS).

---

## FUENTES PRINCIPALES

- W3C WAI — Older Users and Web Accessibility (2025)
- Chiriboga-Casanova, Medina, Paderewski — "Accessible Web Design for Older Adults" (ACM, 2025) / SLR 4.052 artículos
- Springer — "Optimizing mobile app design for older adults" (2025)
- JMIR — "Toward Inclusive Design Heuristics for Digital Health..." (2025)
- UNAD — UX banca para adultos mayores Colombia (2025); entrega medicamentos Tunjuelito (2025)
- U. Distrital + DANE — 83% adultos mayores con smartphone; 95% celular único acceso (2021)
- ECCI — "Incidencia alfabetización y brecha digital en e-commerce" (2025) — 67.4% desconfianza
- UPAEP — "Intención de compra de adultos mayores supermercados digitales" (2025)
- Fenalco/Fenaltiendas — Encuesta Tenderos (2024); NielsenIQ canal predilecto
- La República / Portafolio / El Tiempo — WhatsApp 47% compras sociales; 84.1% tiendas online usan WhatsApp; Tiendanube NubeCommerce 2026; Rappi 25M personas; Treinta 1.4M usuarios
- TiendasdeBarrio.CO, Directoriode.co, MiBarrio, VeciApp, Arequipa.app, Plaixe, ECO App — directorios/competidores
- POLIS (ICESI) — economía informal Cali: 43% informalidad, 36% estratos 1-2, 53% ≤ secundaria
- Cámara de Comercio de Cali — Perfil Económico y Social (2025); estudio económico 2025; micronegocios El Tiempo/ WWB (2025)
- Conversion psychology (2026): cognitive fluency, LIFT, Hick's Law, FOGG, McKnight trust model, social proof

---

*Documento de investigación — Conecta Comuna — 1 sept 2026 — Interno (no subir a GitHub)*

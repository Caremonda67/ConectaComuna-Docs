# Asistente Comunal (IA Vecinal y Accesibilidad)

Fecha: 25 de octubre de 2026  
Módulo: Asistente Comunal con IA y soporte de voz  
Respuesta a: Retroalimentación del jurado de semifinales Fedesoft (Evaluador 2: *"asistente de IA que reemplace al facilitador en algunos escenarios"*)

---

## Qué resuelve este módulo

Para cumplir el objetivo social de inclusión digital de adultos mayores y artesanos que no están acostumbrados a teclear en celulares:
1. **Reemplazo/apoyo al facilitador:** El micro-emprendedor puede dictarle por voz su oficio y sus tarifas. El asistente estructura el título, descripción, categoría y precio estimado, y le permite agregarlo al catálogo con un toque.
2. **Ubicación activa automática:** Si el vecino no dice en qué barrio está, el asistente toma su ubicación activa y calcula distancias exactas a los negocios registrados sin frases robóticas.
3. **Pacto de Trato Seguro:** Explica con claridad la regla del 50% máximo de anticipo para materiales y los mecanismos de mediación vecinal comunitaria.
4. **Accesibilidad completa:**
   - **Voz a texto:** Usa la Web Speech API del navegador (`webkitSpeechRecognition`).
   - **Texto a voz (TTS):** Permite escuchar cada respuesta en voz alta y cuenta con un interruptor para lectura automática para personas con baja visión o dificultades de lectura.
   - **Tarjetas de acción:** Recomendaciones de negocios con botón directo a WhatsApp y enlace al perfil.
   - **Motor híbrido:** Conexión con Google Gemini Flash y motor de respaldo local en Node/TypeScript para funcionar sin internet exterior ni saldo de API.

---

## Mejoras de UX/UI y Flujo Conversacional Multi-Turno

1. **Modo Oscuro Corregido (Alto Contraste):**
   - Eliminadas clases manuales que forzaban texto oscuro en modo oscuro (`dark:text-ink-100`).
   - Se alinearon las tarjetas del chat con los tokens nativos de Tailwind (`card`, `card-soft`, `text-ink-900`, `text-ink-600`), logrando contraste 14:1 contra fondos nocturnos.
2. **Redacción Natural de Ubicación:**
   - Si la etiqueta del GPS o selector es `"Mi ubicación"`, el asistente ya no dice `"en Mi ubicación"`; ahora redacta: `"cerca de tu ubicación actual en la comuna"`.
   - Si es un barrio específico, dice: `"cerca del barrio Antonio Nariño"`.
3. **Flujo de Dictado Guiado:**
   - Al tocar `"🎙️ Dictar mi servicio para el catálogo"`, el asistente no genera una tarjeta falsa; en su lugar, guía amablemente al usuario con ejemplos y lo invita a hablar o escribir su oficio.
4. **Modificación Interactiva de Precios:**
   - Al tocar `"Cambiar el precio"` en una tarjeta de servicio, el asistente pregunta a qué monto desea cambiarlo y ofrece píldoras rápidas (`$15.000`, `$20.000`, `$30.000`, `$40.000`), sin recomendar negocios por error.
   - Al seleccionar o dictar el nuevo monto, actualiza la tarjeta al instante conservando el contexto.
5. **Navegación Enriquecida al Mapa y Secciones:**
   - Al tocar 'Ver en el mapa', el asistente detecta la categoría consultada (ej. modistería, cerrajería) y presenta una tarjeta enriquecida con icono, descripción y botón 'Abrir mapa comunal'.
   - Al pulsarlo, redirige a /mapa?categoria=costura cerrando el diálogo y abriendo el mapa ya filtrado con las opciones del barrio.
6. **Enrutamiento de Intenciones y Gemini 3.5 Flash:**
   - Intenciones directas de botones (mapa, dictado, cambio de precio, Trato Seguro, facilitador) se resuelven en milisegundos mediante el enrutador local de intenciones para respuesta instantánea y sin desvíos.
   - Consultas abiertas y conversacionales se atienden mediante gemini-3.5-flash (con respaldo en gemini-3.5-flash-lite), configurado con thinkingBudget: 0 para respuesta inmediata.

7. **Conciencia Plena de Sesión y Puerta Lógica de Confirmación:**
   - El copiloto recibe en cada mensaje el estado de sesión: si el usuario está autenticado, su nombre, rol activo (`client` o `business`), si ya tiene un negocio registrado y qué pantalla tiene abierta.
   - Si un usuario registrado como Cliente dice *"Quiero crear mi negocio"*, el copiloto **nunca** lo envía a crear cuenta nueva ni cambia su rol a espaldas. En su lugar, despliega una tarjeta de confirmación explícita:
     > *"Vecino [Nombre], actualmente tu cuenta está como Cliente. Para publicar tus oficios y tener tu taller en el mapa, activaremos tu perfil como Emprendedor (podrás seguir pidiendo servicios normalmente). ¿Deseas activar tu perfil de negocio ahora?"*
   - Botones interactivos: `✅ Sí, activar mi negocio` y `❌ No por ahora`.
   - Al confirmar, actualiza su perfil en Supabase, refresca la sesión global y lo dirige a `/panel/negocio` con sus datos precargados.
   - Si el usuario ya tiene un negocio registrado, el botón de dictado guarda el servicio de verdad en su tabla `services_catalog` de Supabase con notificación inmediata.



---

## Estructura técnica implementada

### Backend (`backend/`)
- Ruta: `POST /api/ai/chat` en `backend/src/routes/ai.js`.
- Inyección de negocios reales desde Supabase filtrados por proximidad Haversine.
- Motor local de respaldo para garantizar disponibilidad 24/7 sin dependencias externas.
- Servidor montado en `src/server.js` bajo `/api/ai`.

### Frontend (`frontend/`)
- Botón flotante: `src/components/chat/FloatingChatButton.tsx`.
- Modal/Drawer responsivo: `src/components/chat/AsistenteComunalModal.tsx`.
- Estado global: `src/context/AsistenteContext.tsx` y `src/hooks/useAsistente.ts`.
- Tipos de datos: `src/types/ai.ts`.
- Servicio de comunicación: `src/services/aiService.ts`.
- Iconografía centralizada: `src/components/ui/icons.ts` (`mic`, `micOff`, `volume`, `bot`, `send`).

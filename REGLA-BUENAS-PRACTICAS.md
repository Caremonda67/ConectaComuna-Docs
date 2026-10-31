# REGLA OBLIGATORIA Y PRIMORDIAL: BUENAS PRÁCTICAS, FLUJO LIMPIO Y CÓDIGO HUMANO

Como parte de los criterios de evaluación del Concurso Fedesoft y para asegurar el éxito de Conecta Comuna, **es un requisito OBLIGATORIO Y PRIMORDIAL** en todos los casos:
1. Usar siempre las mejores prácticas de programación.
2. Mantener obligatoriamente un flujo de trabajo limpio.
3. El código debe estar bien construido, de forma natural, sencilla y con estilo 100% humano (nada de código robotizado ni sobre-ingeniería generada por IA).

Cualquiera que trabaje en este repositorio debe ceñirse a las siguientes directrices sin excepción.

---

## 1. Código Natural, Humano y Bien Construido

El código se lee muchas más veces de las que se escribe. El objetivo es crear una experiencia de lectura fluida para cualquier miembro del equipo humano.

- **Nombres Expresivos y Humanos:** Nombra variables, funciones y clases de forma descriptiva (nada de `data1`, `temp`, `x`). El código debe leerse como prosa (ej: `const diasDesdeUltimaVenta`, en lugar de `const d`).
- **No uses "Números Mágicos":** Si tienes una constante como `0.6` o `40`, extráela a una variable con nombre claro (ej: `const UMBRAL_VERIFICACION_FACIAL = 0.6`).
- **Comenta el "Por qué", no el "Qué":** El código bien escrito se explica solo. Los comentarios deben ser escasos y de estilo humano. Úsalos solo para explicar decisiones de negocio difíciles o parches técnicos raros. NUNCA generes comentarios robóticos tipo `// Esta función retorna un booleano`.
- **Estructura Lógica (Storybook):** Ordena los archivos como un artículo: primero los imports, luego la función principal, y abajo las funciones auxiliares.

## 2. Flujo de Trabajo Limpio (Clean Workflow)

- **Regla del "Boy Scout" (Explorador):** Siempre deja el archivo un poco más limpio de lo que lo encontraste. Si ves código muerto, variables sin usar o *console.logs* olvidados, elimínalos.
- **Planeación Antes de Ejecución:** No escribas código a ciegas. Primero piensa, investiga, crea un plan paso a paso y luego implementa.
- **Commits Atómicos y Claros:** Un commit debe hacer una sola cosa y su mensaje debe explicarla claramente en español. Prohibido hacer commits gigantescos que mezclen features y correcciones de bugs.
- **Sin Errores en la Consola:** El servidor no debe arrojar advertencias (*warnings*) ni errores en la terminal. El frontend debe compilar perfecto con `tsc -b` y el linter (`oxlint`).

## 3. Arquitectura y Buenas Prácticas Técnicas

### Frontend (React + TypeScript)
- **Responsabilidad Única (SRP):** Un componente hace una sola cosa. Si crece demasiado, pártelo en componentes más pequeños.
- **Custom Hooks:** Extrae la lógica pesada, las llamadas a API y el manejo de estado a *hooks* personalizados (ej: `useAuth`, `useNeighborhoodLocator`) para mantener la interfaz de usuario limpia.
- **Evita el Prop Drilling:** No pases propiedades a través de 5 niveles de componentes. Si eso pasa, usa Context API o composición.
- **Gestión de Estado:** No guardes en estado de React algo que puedes calcular al vuelo a partir de otras variables.
- **Tipado Fuerte (TypeScript):** Usa interfaces para definir todo (ej: `Producto`, `Negocio`). **Cero tolerancia al uso de `any`**.

### Backend (Node.js + Supabase)
- **Principio DRY (Don't Repeat Yourself):** No copies y pegues lógica. Si haces lo mismo dos veces, conviértelo en una función auxiliar.
- **Estructura en Capas:** Separa las consultas a la base de datos de la lógica de renderizado. 
- **Manejo de Errores Humanos:** Usa `try/catch` de forma inteligente. Nunca muestres un error de SQL o del servidor en la pantalla del usuario. Muestra un mensaje amigable (ej: "No pudimos guardar tu perfil, intenta de nuevo").
- **Variables de Entorno:** Jamás quemes credenciales, tokens ni URLs de bases de datos en el código fuente.

**RESUMEN PARA AGENTES DE IA:** Piensa siempre "como un humano senior pragmático". Evita la sobre-ingeniería masiva, evita los comentarios tipo tutorial, y asegúrate de que cada línea de código tenga un propósito absoluto. Si rompes estas reglas, el proyecto fallará la revisión.

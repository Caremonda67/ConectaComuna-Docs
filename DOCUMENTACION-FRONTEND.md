# Conecta Comuna — Documentación Técnica del Frontend

**Aplicación:** Single Page Application (SPA) Conecta Comuna  
**Entorno y Compilación:** React 19.2 + TypeScript 5.9 + Vite 8.2  
**Estilos y Maquetación:** Tailwind CSS v3.4  
**Enrutamiento:** React Router DOM v7  
**Cartografía y Validación:** Leaflet v1.9 + Verificación Territorial por Facilitadores  
**Despliegue:** Vercel  

---

## 1. Arquitectura de la Aplicación Cliente

La aplicación está diseñada bajo el paradigma de componentes desacoplados, fuertemente tipados y optimizados para dispositivos móviles con pantallas pequeñas y conexiones intermitentes.

### Estructura de carpetas
```text
frontend/src/
├── main.tsx                  # Inicialización y renderizado en DOM
├── router.tsx                # Configuración declarativa de rutas
├── index.css                 # Importación de Tailwind y fuentes
├── types/
│   └── index.ts              # Tipos del dominio (Business, Profile, Order, etc.)
├── lib/
│   ├── env.ts                # Variables de entorno y selector de modo demo
│   ├── supabase.ts           # Cliente Supabase tipado y manejo de sesión
│   ├── ubicacion.ts          # Coordenadas, distancias y geocodificación
│   └── utils.ts              # Formateadores de fecha, moneda y horarios
├── context/
│   └── AuthContext.tsx       # Proveedor global de sesión, perfil y rol activo
├── hooks/
│   ├── useAuth.ts            # Consumo de sesión y acciones de cuenta
│   ├── useGeolocation.ts     # Acceso al GPS del dispositivo con fallbacks
│   └── useNeighborhoodLocator.ts # Identificación automática del barrio
├── services/
│   ├── authService.ts        # Métodos de login, registro y cierre de sesión
│   ├── businessService.ts    # Búsqueda, filtros, fotos y edición de comercio
│   ├── demoBackend.ts        # Almacenamiento local simulado para modo offline
│   ├── demoDataService.ts    # Semillas de demostración con negocios barriales
│   ├── direccionesService.ts # Gestión de direcciones guardadas del cliente
│   ├── facilitadorService.ts # Códigos de vinculación y gestión asistida
│   └── ordersService.ts      # Envío y seguimiento de pedidos
├── components/
│   ├── auth/                 # AuthGate, SocialAuthButtons
│   ├── map/                  # LazyMap, BusinessMap, UbicacionPicker
│   ├── navigation/           # NavBar, BottomNav, RoleSwitch
│   └── ui/                   # Button, Field, Badges, Modal, iconos SVG
└── pages/
    ├── HomePage.tsx          # Portada comunitaria con accesos directos
    ├── ExplorePage.tsx       # Buscador con filtros por oficio y cercanía
    ├── BusinessDetailPage.tsx# Perfil del negocio, productos y botón WhatsApp
    ├── MapPage.tsx           # Vista cartográfica general de la comuna
    ├── HowItWorksPage.tsx    # Guía explicativa para vecinos y facilitadores
    ├── TratoSeguroPage.tsx   # Pacto de confianza comunal y 5 acuerdos de seguridad
    ├── OnboardingPage.tsx    # Asistente inicial tras el primer ingreso
    ├── FacilitatorDashboard.tsx # Panel de apadrinamiento comunitario
    ├── auth/                 # LoginPage, RegisterPage
    ├── business/             # DashboardPage, BusinessProfileEditor
    ├── client/               # ClientDashboard
    └── legal/                # TermsPage (Ley 1480 Art 53), PrivacyPage (Ley 1581)
```

---

## 2. Decisiones Técnicas de Arquitectura (ADRs)

### ADR-F01: Cumplimiento estricto del compilador de React 19
- **Contexto:** En versiones anteriores se detectaron advertencias de linter (`react(set-state-in-effect)`) al actualizar estados de formularios de forma síncrona dentro de `useEffect`, lo cual provocaba renderizados en cascada e impedía que el nuevo compilador de React 19 memorizara componentes automáticamente.
- **Decisión:** Refactorizamos los componentes de edición (como `BusinessProfileEditor`) extrayendo el formulario a un componente hijo `BusinessProfileEditorForm` controlado por clave declarativa (`key={activeBusiness?.id ?? 'nuevo'}`).
- **Consecuencias:** Cuando el negocio activo cambia o termina de cargarse desde el servidor, React destruye y remonta el formulario con su estado inicial en `useState`, eliminando todos los efectos secundarios síncronos. La base de código alcanza 0 advertencias en `oxlint`.

### ADR-F02: Modo Dual (Conexión Supabase + Respaldo Local `demoBackend`)
- **Contexto:** En ferias de emprendimiento o presentaciones ante jurados, las redes móviles pueden ser inestables o no disponer de conexión a internet.
- **Decisión:** Implementamos una bandera en `src/lib/env.ts` (`isDemoMode`). Si las credenciales de Supabase no están presentes o se activa el modo de prueba, los servicios redirigen las lecturas y escrituras hacia `demoBackend.ts`, el cual simula la persistencia en `localStorage` con retardos asíncronos idénticos a los de una red real.
- **Consecuencias:** La plataforma nunca se bloquea ni muestra pantallas en blanco ante fallos de conexión externa.

### ADR-F03: Cartografía ligera con carga diferida (`LazyMap`)
- **Contexto:** Las librerías de mapas pesadas pueden duplicar el peso del paquete JavaScript y congelar teléfonos de gama baja al abrir la portada.
- **Decisión:** Usar Leaflet junto a los mosaicos libres de OpenStreetMap, encapsulando la inicialización del mapa dentro de `LazyMap.tsx` mediante `React.lazy` y `Suspense`. Los polígonos y pines solo se cargan cuando el usuario ingresa a la pestaña del mapa o abre el selector de dirección.
- **Consecuencias:** El bundle inicial se reduce en más de 160 kB, acelerando la carga inicial en redes móviles 3G o 4G débiles.

### ADR-F04: Verificación Territorial por Facilitador Comunitario
- **Contexto:** En la Fase 0 (entrevistas con Doña Marta, Don Ricardo y Alex David) la barrera principal fue la desconfianza del cliente. Antes de decidir probamos un cotejo automático de identidad en el navegador y no aguantó el parque de dispositivos del barrio: gama baja, sin cámara frontal nítida y con datos prepago.
- **Decisión:** La confianza se entrega con el **Sello de Verificación en Territorio**, una visita presencial del facilitador al taller o local. Queda registrada en la base con quién la otorgó, la fecha y una nota.
- **Consecuencias:** Cero fricción técnica para el emprendedor, validación humana de alguien del barrio y funcionamiento en conexiones lentas.

### ADR-F05: Enfoque mobile-first y accesibilidad barrial
- **Contexto:** Los datos de campo evidenciaron que el 50% de los emprendedores tiene dificultades con las aplicaciones complejas.
- **Decisión:** Establecer una altura mínima de 44px en todos los botones y campos interactivos, utilizar tipografía de alto contraste (paleta `ink` sobre fondo `cream`), evitar menús ocultos de varios niveles y proporcionar botones directos con el icono oficial de WhatsApp para coordinar pedidos con un solo toque.

---

## 3. Mapa de Rutas y Navegación

El enrutamiento está centralizado en `src/router.tsx` mediante `createBrowserRouter`:

| Ruta | Componente | Acceso | Propósito |
|---|---|---|---|
| `/` | `HomePage` | Público | Portada con oficios destacados, barra de búsqueda y accesos rápidos. |
| `/explorar` | `ExplorePage` | Público | Listado completo de negocios con filtro por categoría, calificación y radio. |
| `/negocio/:id` | `BusinessDetailPage` | Público | Ficha completa del emprendedor, fotos, productos, horarios y botón WhatsApp. |
| `/mapa` | `MapPage` | Público | Mapa interactivo con la ubicación aproximada de los comercios del sector. |
| `/como-funciona` | `HowItWorksPage` | Público | Los dos recorridos —quien busca y quien ofrece— y el ciclo de un pedido. |
| `/tecnologia` | `TecnologiaPage` | Público | La arquitectura en lenguaje llano y cómo comprobar cada decisión en vivo. |
| `/trato-seguro` | `TratoSeguroPage` | Público | Los cinco acuerdos del pacto comunal y el alcance real del respaldo. |
| `/trato-seguro/verificar` | `TratoSeguroVerificationPage` | Público | Consulta de un comprobante por código, sin necesidad de cuenta. |
| `/trato-seguro/verificar/:codigo` | `TratoSeguroVerificationPage` | Público | El mismo verificador con el código ya cargado desde el enlace o el QR. |
| `/terminos` | `TermsPage` | Público | Términos del servicio. |
| `/privacidad` | `PrivacyPage` | Público | Política de tratamiento de datos. |
| `/entrar` | `LoginPage` | Público | Inicio de sesión con correo/contraseña o acceso social. |
| `/registro` | `RegisterPage` | Público | Formulario de registro seleccionando rol (`client`, `business`, `facilitador`). |
| `/onboarding` | `OnboardingPage` | Autenticado | Asistente de configuración de perfil y datos barriales. |
| `/verificacion` | — | Autenticado | Redirección a `/panel`; se conserva por enlaces antiguos. |
| `/panel` | `DashboardPage` | Autenticado | Reparte según el rol activo: cliente, negocio o facilitador. |
| `/panel/negocio` | `BusinessProfileEditor` | Negocio / Facilitador con acceso | Ficha del negocio. Requiere estar en modo Negocio; el facilitador debe indicar qué negocio edita (`?id=`) y no puede crear uno propio. |
| `/panel/facilitador` | `FacilitatorDashboard` | Facilitador | Negocios apadrinados, vinculación por código y firma del sello en territorio. |
| `/panel/equipo` | `EquipoPage` | Autenticado | Equipo del proyecto. |

---

## 4. Sistema de Diseño y Tokens Visuales

El diseño visual se apoya en Tailwind CSS y respeta la identidad cálida de los barrios populares colombianos:

### Paleta cromática oficial (`src/index.css`)

No hay `tailwind.config.ts`: con Tailwind v4 los tokens se declaran en un
bloque `@theme` dentro de `src/index.css`, y el modo oscuro los **redefine
enteros** bajo `html.dark`.

- **`brand` (verde de huerta):**
  - `brand-500`: `#6fb257` (acento y bloques destacados).
  - `brand-700`: `#467a36` (botones primarios: es el tono que alcanza 5,13:1 con texto blanco).
  - `brand-50`: `#f2f9ef` (fondos sutiles de selección).
- **`ink` (neutros cálidos, derivados del negro de la paleta):**
  - `ink-900`: `#1a1a1a` (títulos y texto principal).
  - `ink-500`: `#6b6b65` (texto secundario; es el mínimo que cumple AA en los dos temas).
  - `ink-200`: `#d9d7cc` (bordes de tarjetas y separadores).
- **`cream` (fondo cálido comunitario):**
  - `cream-100`: `#f9f8f2` (fondo general, para evitar el blanco frío).
  - `cream-50`: `#fdfdfa` (superficies elevadas).
- **Estados** (escalas de Tailwind, no propias): `emerald` para "Abierto ahora"
  y el sello verificado, `amber` para pendientes y avisos, `rose` para errores.

**La trampa de las cuatro escalas propias.** `brand`, `ink`, `cream` y `accent`
se redefinen enteras en modo oscuro, así que el token ya se invierte solo y
escribir `dark:text-brand-300` encima pone texto oscuro sobre fondo oscuro. Y
la inversión **no es un espejo**: en oscuro, `brand-50…300` son fondos oscuros
y `brand-400…950` son textos claros. Por eso un fondo `brand-700` necesita
texto blanco en claro y texto oscuro en oscuro —la única excepción legítima a
la regla—. Las escalas de Tailwind (`emerald`, `amber`, `rose`) no se
invierten y ahí el `dark:` sí hace falta. Está medido en
[[EVIDENCIA-CONTRASTE-WCAG]].

### Iconografía y micro-interacciones
- Los iconos se centralizan en `src/components/ui/icons.ts`, que reexporta los de
  `lucide-react` agrupados por uso (`UI_ICONS`, `CATEGORY_ICONS`, `BADGE_ICONS`).
  Los componentes importan de ahí y no de la librería, para poder sustituir el
  set entero tocando un solo archivo.
- El conmutador de rol vive dentro de `components/layout/AppLayout.tsx` (no hay
  un `RoleSwitch.tsx` aparte) y deja a la cuenta de negocio o de facilitador
  alternar con la vista de cliente sin cerrar sesión.

---

## 5. Gestión del Estado y Servicios Desacoplados

### `AuthContext.tsx`
El contexto de autenticación administra de forma centralizada:
- `user`: Datos del usuario autenticado en Supabase.
- `profile`: Registro de la tabla `profiles` (nombre completo, teléfono, barrio, `account_type`).
- `business`: Emprendimiento perteneciente al usuario (si aplica).
- `activeRole`: Rol actual en pantalla (`client`, `business` o `facilitador`), facilitando la experiencia dual.
- `refresh()`: Función para reconsultar y actualizar el estado tras modificaciones en el perfil.

### Capa de Servicios
Toda la lógica de comunicación HTTP o consultas a base de datos se mantiene fuera de los componentes visuales:
- **`businessService.search(filters)`:** Construye consultas con ordenamiento por calificación, novedad o distancia euclidiana en kilómetros.
- **`facilitadorService.vincularNegocio(codigo)`:** Conecta a un facilitador con un negocio mediante código de verificación presencial.
- **`ordersService.create(order)`:** Genera solicitudes de pedido con estado inicial pendiente.

---

## 6. Pruebas de Calidad, Rendimiento y Despliegue

### Resultados de la auditoría técnica
- **Linter (`oxlint src`):** **0 advertencias, 0 errores** en los 60 archivos del proyecto. Se eliminaron antipatrones de `set-state-in-effect`.
- **Compilador TypeScript (`tsc -b`):** **0 errores**. Todos los contratos de props y retornos de funciones cuentan con tipado estricto.
- **Compilación de producción (`vite build`):**
  - Tiempo de construcción: **4.4 segundos**.
  - Fragmentación de dependencias (vendor chunking): Los módulos pesados (`react`, `supabase`, `map`) se dividen en archivos separados que el navegador almacena en caché de forma independiente, garantizando tiempos de carga inferiores a 1.5 segundos en visitas recurrentes.

### Configuración para Vercel (`vercel.json`)
La aplicación incluye reglas de reescritura para gestionar el enrutamiento del lado del cliente (SPA):
```json
{
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ]
}
```
Esto asegura que rutas profundas como `/negocio/7c9e6679` se resuelvan correctamente al recargar la página.

# WIREFRAMES LOW-FI — MVP Conecta Comuna

> Fecha: 1 sept 2026 · Estado: **v1 (borrador)**
> Formato: textual (baja fidelidad). Se convertirán a Figma (alta fidelidad) cuando se valide estructura y flujos.
> **Nota:** estas pantallas son del MVP. Según avance la investigación de campo (Fase 0), se analiza si se agregan más pantallas o flujos.
> Convenciones: `[Botón]` | `(Input)` | `# H1` | `## H2` | valores entre `{}` | comentarios con `//`

---

## Índice de pantallas (MVP)

| ID | Pantalla | Rol principal | Ruta propuesta |
|----|----------|---------------|----------------|
| W-01 | Home / Directorio | Cliente / visitante | `/` |
| W-02 | Resultados de búsqueda | Cliente | `/buscar` |
| W-03 | Detalle de emprendimiento | Cliente | `/emprendimiento/:id` |
| W-04 | Detalle de producto/servicio | Cliente | `/producto/:id` |
| W-05 | Registro / Alta de emprendimiento | Emprendedor + Facilitador | `/registro` |
| W-06 | Iniciar sesión | Todos | `/login` |
| W-07 | Panel del emprendedor (dashboard) | Emprendedor | `/panel` |
| W-08 | Publicar / Editar producto | Emprendedor | `/panel/nuevo-producto` |
| W-09 | Panel del facilitador | Facilitador | `/facilitador` |

Flujos clave: **Descubrir (W-01→W-02→W-03→W-04) · Vender (W-05→W-07→W-08) · Pedir (W-03/W-04→WhatsApp) · Asistir y moderar (W-05→W-09)**

---

### W-01 — HOME / DIRECTORIO
**Propósito:** primera impresión + entrada al directorio. Mobile-first (celular).
**Rol:** Cliente y visitante.

```
┌──────────────────────────────┐
│ [menu] CONECTA COMUNA [buscar] │ Header fijo
│ "El directorio de tu barrio" │
├──────────────────────────────┤
│ ([buscar] Buscar emprendimiento...)│ Buscador global
├──────────────────────────────┤
│ CATEGORÍAS (chips) │
│ [Servicios] [Comidas] │
│ [Artesanías] [Moda] │
│ [Tecnología] [Otros] [ver+ ]│
├──────────────────────────────┤
│ EMPRENDIMIENTOS DESTACADOS│
│ ┌──────────┬──────────┐ │
│ │[foto] │[foto] │ │ Cards 2xn (grid)
│ │Panadería │ Costura │ │
│ │*** │ **** │ │
│ │[llamar] WhatsApp│ [llamar] │ │
│ └──────────┴──────────┘ │
│ [Cargar más...] │
├──────────────────────────────┤
│ CÓMO FUNCIONA (3 pasos) │
│ 1. Busca 2. Contacta 3. Compra local │
├──────────────────────────────┤
│ [Footer: Acerca de | Contacto │
│ | Categorías | Ayuda] │
└──────────────────────────────┘
```
**Elementos clave:** buscador siempre a la vista, categorías en chips, destacados con foto+rating+WhatsApp, flujo guiado de "cómo funciona".
**Nota:** La investigación dirá si el home debe priorizar "destacados" vs "más cercanos" (usar ubicación aproximada).

---

### W-02 — RESULTADOS DE BÚSQUEDA
**Propósito:** filtrar y encontrar el emprendimiento adecuado.
**Rol:** Cliente.

```
┌──────────────────────────────┐
│ [←] Resultados "panadería" │
├──────────────────────────────┤
│ FILTROS (desplegable) │
│ Categoría ▾ Comuna ▾ │
│ Precio: [$-$$-$$$] Disponible hoy [ok] │
├──────────────────────────────┤
│ [Lista] [Mapa] │ Toggle lista/mapa
│ ┌──────────────────────────┐ │
│ │[foto] Panadería La Esquina │ │ Item resultado
│ │ **** (12) · 400m │ │
│ │ Pan, pastelería │ │
│ │ [Contactar] [Ver] │ │
│ └──────────────────────────┘ │
│ ┌──────────────────────────┐ │
│ │[foto] Repostería Mary │ │
│ │ *** (8) · 1.2km │ │
│ │ Postres, tortas │ │
│ │ [Contactar] [Ver] │ │
│ └──────────────────────────┘ │
├──────────────────────────────┤
│ Resultados: 14 · [Cargar más]│
└──────────────────────────────┘
```
**Elementos clave:** filtro por categoría/comuna/precio/disponibilidad; lista o mapa (Leaflet). Distancia aproximada si hay geolocalización.
**Decisión por investigar:** ¿mapa es MVP o post-MVP? (RF-10 ubicación aproximada está en requisitos, pero pesa UX).

---

### W-03 — DETALLE DE EMPRENDIMIENTO
**Propósito:** ficha pública del negocio + acceso a catálogo y contacto.
**Rol:** Cliente.

```
┌──────────────────────────────┐
│ [←] [⋮] │
│ ┌──────────────────────────┐ │
│ │ [Foto de portada] │ │
│ │ (Carrusel ◀ ▶) │ │
│ └──────────────────────────┘ │
│ PANADERÍA LA ESQUINA │
│ **** (12 reseñas) │
│ Panadería · [pin] Comuna 2, │
│ Calle 8 #4-15 (aprox.) │
│ [reloj] Lun-Sáb 6am-7pm · Hoy [ok]│
│ Idioma simple: explica │
│ fácil · Atiende pedidos │
├──────────────────────────────┤
│ [WhatsApp] [Llamar] │ Acciones primarias
│ [Favorito] │
├──────────────────────────────┤
│ CATÁLOGO │
│ ┌──────────────────────────┐ │
│ │Pan tajado...........$3.5k│ │
│ │[ver] │ │
│ ├──────────────────────────┤ │
│ │Torta cumple.........$45k │ │
│ │[ver] │ │
│ ├──────────────────────────┤ │
│ │[Ver catálogo completo] │ │
│ └──────────────────────────┘ │
├──────────────────────────────┤
│ FACILITADOR: │
│ "Si necesitas ayuda para │
│ pedir, contáctame" [Chatear] │ (si aplica)
├──────────────────────────────┤
│ [pin] Mapa (OpenStreetMap) │
└──────────────────────────────┘
```
**Elementos clave:** foto, rating, categoría, ubicación aprox, disponibilidad, **un solo botón WhatsApp dominante**, catálogo inline, botón favorito.
**Contenido accesible:** lenguaje simple (uso ético TIC / inclusión).

---

### W-04 — DETALLE DE PRODUCTO/SERVICIO
**Propósito:** ver precio, características y hacer pedido.
**Rol:** Cliente.

```
┌──────────────────────────────┐
│ [←] [] │
│ ┌──────────────────────────┐ │
│ │ [Foto producto] │ │
│ └──────────────────────────┘ │
│ PAN TAJADO │
│ Panadería La Esquina **** │
├──────────────────────────────┤
│ $3.500 │ RF-17 precios visibles
│ Precio por mayor: $3.000 │ RF-11 venta al mayor
│ (min 10 unidades) │
├──────────────────────────────┤
│ Descripción: │
│ Pan blanco tajado, rinde para│
│ desayuno. Hecho el mismo día.│
│ [Ver foto grande] │
├──────────────────────────────┤
│ Disponibilidad: [ok] Hoy │
│ [no] Mañana (no disponible) │
├──────────────────────────────┤
│ ENVÍO / ENTREGA: │
│ ( ) Recojo en el local │
│ ( ) Entrega en mi comuna +$2k│
├──────────────────────────────┤
│ [Pedir por WhatsApp] <-- │ PRIMARIO: abre WhatsApp
│ con mensaje precargado: │ "Hola, quiero 2 pan tajado"
├──────────────────────────────┤
│ RESUMEN DE PEDIDO: 2 x $3.5k │
│ Total: $7.000 │
│ [Confirmar pedido] │
├──────────────────────────────┤
│ R E S E Ñ A S │
│ ***** "Delicioso" -Ana │ RF-16 reseñas
│ **** "Buen precio" -Luis │
│ [Ver todas las reseñas] │
└──────────────────────────────┘
```
**Elementos clave:** precio visible (RF-17), opción venta al por mayor (RF-11), disponibilidad por día, **pedido directo por WhatsApp con mensaje precargado**, reseñas (RF-16). Preliminar: opción recojo vs entrega.

---

### W-05 — REGISTRO / ALTA DE EMPRENDIMIENTO
**Propósito:** dar de alta un negocio. Dos caminos: el emprendedor mismo, o el facilitador en su nombre (RF-12).
**Rol:** Emprendedor / Facilitador.

```
┌──────────────────────────────┐
│ [←] CREA TU EMPRENDIMIENTO │
│ "Conectamos tu negocio con tu│
│ comuna - gratis" │
├──────────────────────────────┤
│ ¿Quién crea la cuenta? │
│ (•) Soy el emprendedor │
│ ( ) Un facilitador me ayuda │
├──────────────────────────────┤
│ DATOS DEL NEGOCIO │
│ (Nombre del negocio) │
│ (Categoría ▾) │
│ (Descripción breve) │
│ (Comuna ▾) · (Dirección aprox)│
│ (Teléfono / WhatsApp) │
│ ([foto] Subir foto de negocio) │
├──────────────────────────────┤
│ TU ACCESO (si es dueño) │
│ (Nombre) (Celular) │
│ (Contraseña) │
├──────────────────────────────┤
│ [ok] Acepto términos y │
│ compromiso de calidad │
├──────────────────────────────┤
│ [ CREAR MI PERFIL] │
│ Tiempo: ~3 minutos │
├──────────────────────────────┤
│ "Ya tienes perfil? [Entrar]" │
└──────────────────────────────┘
```
**Elementos clave:** flujo único para dueño y facilitador; campos mínimos; UNA sola foto obligatoria al inicio (baja barrera); tiempo estimado para transmitir simplicidad.
**Decisión por investigar:** ¿verificación de identidad (cédula) es MVP o post? Influencia en confianza pero agrega fricción.

---

### W-06 — INICIAR SESIÓN
**Propósito:** entrar como emprendedor, facilitador o admin.
**Rol:** Todos.

```
┌──────────────────────────────┐
│ [←] │
│ CONECTA COMUNA │
│ (logo) │
├──────────────────────────────┤
│ (Celular o usuario) │
│ (Contraseña) │
│ [Mostrar/ocultar] │
│ [ ENTRAR] │
│ [¿Olvidaste tu contraseña?] │
├──────────────────────────────┤
│ ─────── o ─────── │
│ [Entrar como cliente (sin │
│ cuenta) → seguir explorando]│ Baja barrera
└──────────────────────────────┘
```
**Elementos clave:** exploración anónima permitida; login solo para emprendedores y facilitadores al inicio (cliente valorado como anónimo; reseñas/favoritos requieren cuenta `cliente` — RF).

---

### W-07 — PANEL DEL EMPRENDEDOR (DASHBOARD)
**Propósito:** gestionar el negocio: productos, disponibilidad, contacto y actividad.
**Rol:** Emprendedor.

```
┌──────────────────────────────┐
│ [←] Hola, {Nombre}│
│ Mi emprendimiento │
│ Panadería La Esquina [editar]│
├──────────────────────────────┤
│ ESTADO: VISIBLE EN EL │
│ DIRECTORIO [Cambiar] │ toggle público/oculto
├──────────────────────────────┤
│ MIS PUBLICACIONES (4) │
│ [+ Nuevo producto] │
│ ┌──────────────────────────┐ │
│ │[foto] Pan tajado $3.5k │ │
│ │ [ok] Disponible [Editar] │ │
│ ├──────────────────────────┤ │
│ │[foto] Torta $45k [no] Agotado │ │
│ │ [Editar] │ │
│ └──────────────────────────┘ │
├──────────────────────────────┤
│ ACTIVIDAD RECIENTE │
│ · 12 vistas esta semana │
│ · 5 mensajes de WhatsApp │ Estadísticas simples
│ · 3 favoritos │ RF-18 panel emprendedor
├──────────────────────────────┤
│ CHAT (2 no leídos) │ RF-18 chat
│ [Responder con clientes] │
├──────────────────────────────┤
│ [ajustes] Ajustes del perfil │
│ · Fotos · Horarios · Datos │
└──────────────────────────────┘
```
**Elementos clave:** toggle de visibilidad (control total), gestión de productos con estado, estadísticas mínimas, acceso a chat (RF-18). Lenguaje claro, íconos grandes.

---

### W-08 — PUBLICAR / EDITAR PRODUCTO
**Propósito:** alta rápida de producto o servicio con foto, precio y disponibilidad.
**Rol:** Emprendedor (o facilitador en su nombre).

```
┌──────────────────────────────┐
│ [←] NUEVO PRODUCTO │
├──────────────────────────────┤
│ ([foto] Foto del producto) │
│ [Subir foto] (obligatoria)│
├──────────────────────────────┤
│ (Nombre del producto) │
│ (Categoría ▾) │
│ (Descripción breve - 2 líneas)│
├──────────────────────────────┤
│ ¿Es producto o servicio? │
│ (•) Producto ( ) Servicio │
├──────────────────────────────┤
│ $ Precio unitario ($) │
│ Precio por mayor ($) min ( )│ RF-11 (opcional)
├──────────────────────────────┤
│ DISPONIBILIDAD │
│ Días: [L][M][X][J][V][S][D] │
│ ¿Tienes stock hoy? [ok] Sí]/[ ]│
├──────────────────────────────┤
│ [ GUARDAR PUBLICACIÓN] │
└──────────────────────────────┘
```
**Elementos clave:** foto obligatoria pero única; precio unitario + opcional mayorista; disponibilidad por días + toggle "disponible hoy"; minutos para completar.

---

### W-09 — PANEL DEL FACILITADOR
**Propósito:** apoyar a emprendedores poco digitales: crear y editar sus perfiles (RF-12).
**Rol:** Facilitador (aprobado por admin).

```
┌──────────────────────────────┐
│ [←] Hola, {Facilitador}│
│ "Tu apoyo en la comuna" │
├──────────────────────────────┤
│ SOLICITUDES PENDIENTES (2)│
│ ┌──────────────────────────┐ │
│ │[pendiente] Don Alfonso (panadería) │ │
│ │ "Quiere crear su perfil" │ │
│ │ [Atender] │ │
│ ├──────────────────────────┤ │
│ │[pendiente] Doña Marta (costura) │ │
│ │ [Atender] │ │
│ └──────────────────────────┘ │
├──────────────────────────────┤
│ EMPRENDIMIENTOS A CARGO (5)│
│ ┌──────────────────────────┐ │
│ │Panadería La Esquina │ │
│ │ [Editar] [Mensaje] │ │
│ ├──────────────────────────┤ │
│ │Repostería Mary │ │
│ │ [Editar] [Mensaje] │ │
│ └──────────────────────────┘ │
├──────────────────────────────┤
│ [+] Dar de alta nuevo │
│ emprendimiento] │ → flujo W-05 en nombre
└──────────────────────────────┘
```
**Elementos clave:** lista de solicitudes, emprendimientos a cargo, edición asistida, alta en nombre del emprendedor. El facilitador NO tiene productos propios.

---

## FLUJOS PRINCIPALES (resumen)

1. **Descubrir y pedir (Cliente):** `W-01 Home` → busca o navega por categorías → `W-02 Resultados` → `W-03 Detalle` → `W-04 Producto` → botón WhatsApp con mensaje precargado → cierra el pedido en WhatsApp.
2. **Emprender (Emprendedor):** `W-05 Registro` → `W-07 Dashboard` → `W-08 Publicar productos` → toggle "visible" activado.
3. **Apoyar (Facilitador):** `W-05 Registro` (en nombre del emprendedor) → `W-09 Panel` → gestiona productos/ediciones del emprendedor.
4. **Moderar (Facilitador):** `W-06 Login` → `W-09 Panel` → revisa los reportes de los vecinos y deja la nota de moderación.

## PREGUNTAS ABIERTAS (decidir con investigación)
- [ ] ¿El mapa (Leaflet) es MVP o post-MVP? ¿Cuánta ubicación se muestra (aproximada)?
- [ ] ¿Verificación de identidad (cédula) en registro? ¿Solo facilitador la valida?
- [ ] ¿Cuenta "cliente" es necesaria para reseñar/favoritos en MVP? (aus vs. WhatsApp-pedido anónimo)
- [ ] ¿Chat interno (RF-18) es MVP? Alternativa: todo viaja por WhatsApp primero.
- [ ] ¿Venta al por mayor (RF-11) se muestra como precio simple o como flujo de cotización?
- [ ] ¿Sistema de rangos/insignias (RF-16) visible en MVP, o solo revisar en perfiles?
- [ ] ¿El home prioriza destacados, cercanos o mezcla? (depende de geolocalización)
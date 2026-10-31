# Evidencia de contraste WCAG AA

Fecha de la medición: 31 de octubre de 2026.
Método: script en el navegador sobre la aplicación corriendo, que recorre cada
elemento de texto visible, compone todos los fondos de la cadena (incluida la
transparencia y los fondos pintados por hermanos absolutos) y compara la razón
de contraste contra el mínimo que corresponde al tamaño y grosor de esa letra:
**4,5:1** para texto normal y **3:1** para texto grande o negrita ≥ 18,66 px.

Alcance: 11 rutas públicas, **1 526 elementos de texto**, en los dos temas.

## Resultado

| Tema | Elementos | Por debajo del mínimo |
|---|---|---|
| Claro | 1 526 | **0** |
| Oscuro | 1 526 | **0** |

Rutas cubiertas: `/`, `/explorar`, `/mapa`, `/como-funciona`, `/tecnologia`,
`/trato-seguro`, `/trato-seguro/verificar`, `/entrar`, `/registro`,
`/terminos`, `/privacidad`.

## Qué estaba mal y qué se cambió

### El botón primario fallaba en los dos temas

Era `bg-brand-500` con texto blanco.

| | Claro | Oscuro |
|---|---|---|
| Antes (`brand-500` + blanco) | 2,57:1 | 2,53:1 |
| Después (`brand-700` + blanco / `brand-50`) | **5,13:1** | **9,71:1** |

Es el botón más repetido de la aplicación, así que el arreglo está en
`components/ui/Button.tsx` y se propaga solo.

### La escala de marca no es un espejo entre temas

Este es el detalle que más confusión causó, y conviene dejarlo escrito:

```
brand en claro:   50 …… 950   de claro a oscuro
brand en oscuro:  50 … 300    fondos OSCUROS   (#142319 … #2d4f37)
                 400 … 950    textos CLAROS    (#7cc386 … #f2fcf4)
```

Consecuencias prácticas:

1. Un fondo `brand-700` necesita texto **blanco en claro** y **oscuro en
   oscuro**. Es la única excepción legítima a la regla de no poner variantes
   `dark:` sobre la paleta propia: lo que se invierte es la polaridad del par,
   no el tono suelto.
2. No todas las variantes `dark:text-*` sobre la paleta propia están mal. De
   las 46 que existían, **32 eran correctas** (apuntaban a la banda clara) y
   solo **14 estaban rotas** (apuntaban a la banda oscura, dejando texto
   oscuro sobre fondo oscuro, entre 1,8:1 y 2,4:1).

### Lo corregido, por familia

| Familia | Casos | Arreglo |
|---|---|---|
| `dark:text-{ink-300,ink-400,brand-300}` sobre fondo oscuro | 19 | Borrar la variante: la clase base ya da el tono correcto al invertirse |
| `bg-brand-500` + blanco | 11 | Subir a `brand-700` y añadir `dark:text-brand-50` |
| `bg-brand-600` + blanco (burbujas del chat, botones) | 13 | Igual |
| Píldora de categoría activa en Explorar | 1 | `brand-500` → `brand-700`; el fondo lo pinta un hermano absoluto |
| Números de paso en «Cómo funciona» | 1 | `brand-600` (3,42:1) → `brand-700` |
| Pie de página, línea de copyright | 1 | `ink-400` (3,25:1) → `ink-500` (5,36:1) |
| Subtítulo del botón flotante | 1 | Blanco al 80% (3,92:1) → color pleno |

## Advertencias para quien repita la medición

Tres trampas, las tres sufridas durante este trabajo:

1. **No cambies el tema con JavaScript y midas acto seguido.** El fondo se
   actualiza antes que el color del texto y las lecturas salen falseadas: así
   aparecieron 13 «fallos» en el pie y la barra inferior que no existían.
   Recarga la página con el tema ya puesto.
2. **Tailwind v4 devuelve `oklch(...)`.** Leer esos tres números como si
   fueran RGB inventa fallos. Hay que convertir el color de verdad, por
   ejemplo pintándolo en un lienzo de 1 px y leyendo el píxel.
3. **Compón la transparencia contra el fondo real.** Un `bg-white/70`
   compuesto contra negro da un gris oscuro y marca como ilegible una
   cabecera perfectamente legible.

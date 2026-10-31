# Investigación: Frontend fluido, cálido y sin trabas en el barrio

> Fecha: 22 de septiembre de 2026.
> Contexto: Pruebas en celulares reales por red local y ajuste para la entrega Fedesoft (categoría Inclusión y Bienestar).

Durante las pruebas de uso en celular encontramos dos problemas que cortan la experiencia de tajo: el carrusel de categorías que parece congelado en "Belleza" y el botón de ubicación que tira error en el teléfono. A la par, la interfaz se siente un poco "tiesa", como si fuera un formulario de oficina vieja y no un directorio barrial dinámico.

Esta nota recoge qué causó cada fallo técnico, cómo arreglarlos de raíz y qué cambios visuales y de interacción necesitamos para que la app se sienta viva, suave y rápida sin saturar a los vecinos ni reventar la memoria de teléfonos baratos.

---

## 1. Diagnóstico de los fallos encontrados

### A. El carrusel de oficios se corta en "Belleza" y no avanza
- **Qué pasa en pantalla:** Al entrar a `/explorar`, el usuario ve: "Todos", "Costura", "Manicure", "Cerrajería", "Ropa", "Ambulante" y "Belleza". De ahí en adelante parece que no hubiera nada más. Si intentas arrastrar con el mouse o con el dedo sin un gesto horizontal perfecto, no se mueve.
- **La causa real:**
  1. El contenedor mide 345 px de ancho en pantalla móvil, pero adentro hay 12 categorías que suman 2.008 px de ancho total.
  2. Teníamos puesto `scrollbarWidth: none`. Eso borra la barra de desplazamiento por completo. Para el ojo humano, si no hay barra ni flechas ni sombra que se desvanezca al borde, la lista parece haber terminado en "Belleza".
  3. No hay soporte para arrastrar con el mouse en computadores o portátiles (drag-to-scroll).
  4. Los botones `<button>` de cada categoría atrapan el toque y a veces confunden el deslizamiento del dedo con un clic, bloqueando la inercia del scroll en Android.

### B. "Buscar con mi ubicación actual" lanza error
- **Qué pasa en pantalla:** El vecino o cliente toca "Usar mi ubicación" y sale un texto rojo diciendo que no se pudo acceder o que falló la ubicación.
- **La causa real:**
  1. **La regla de contexto seguro del navegador:** Cuando probamos la app desde el celular abriendo la IP del computador por Wi-Fi (tipo `http://192.168.1.15:5173`), Chrome y Safari en Android y iPhone **bloquean por seguridad el GPS (`navigator.geolocation`)**. Los navegadores solo permiten GPS en `https://` o en `http://localhost`. En cualquier otra IP local, la llamada al GPS es rechazada de inmediato con código de error de permiso denegado.
  2. **Permisos previamente denegados:** Si una persona le dio "Bloquear" una vez al aviso del navegador, `navigator.geolocation.getCurrentPosition` falla al instante y el código actual no daba ninguna alternativa, dejándolo sin opciones.
  3. **Caída de Nominatim en geocodificación inversa:** En `useNeighborhoodLocator`, si el GPS respondía pero Nominatim tardaba o devolvía un formato sin barrio, la promesa fallaba y mostraba un error que alarmaba al usuario en pleno registro.

---

## 2. Cómo resolver los dos fallos técnicos

### Solución para el carrusel de categorías
1. **Botones de flecha sutiles (`<` y `>`):** Colocar botones circulares en los extremos que se activan solo cuando hay contenido oculto en esa dirección. Al tocarlos, la barra se desplaza suavemente 200 px con `scrollBy({ behavior: 'smooth' })`.
2. **Efecto de desvanecimiento (fade mask):** Aplicar una sombra degradada suave al extremo derecho mientras queden elementos ocultos. Así el vecino ve claramente que la última pastilla se corta con transparencia y sabe al instante que hay más opciones.
3. **Arrastre con mouse (Drag-to-scroll):** Implementar captura de eventos `onMouseDown`, `onMouseMove` y `onMouseUp` con cambio de cursor a `grab` y `grabbing`. Quien use ratón o trackpad puede arrastrar la lista como si fuera una pantalla táctil.
4. **Respeto a la inercia táctil:** Añadir `-webkit-overflow-scrolling: touch` y `touch-action: pan-x` para que en celulares no se trabe el dedo al rozar un botón.

### Solución para la ubicación
1. **Detección clara de la causa del fallo:**
   - Si no hay HTTPS (IP local) o el usuario negó el permiso, no mostramos un mensaje de error seco.
   - Mostramos un mensaje claro y pedagógico: *"No tenemos acceso al GPS de tu celular (en redes locales o si bloqueaste los permisos). Puedes elegir tu barrio en 1 toque:"*.
2. **Acceso rápido por barrios en 1 toque:**
   - Justo debajo del botón de GPS, colocar accesos directos a los barrios principales de la comuna:
     `[Antonio Nariño]` `[El Rodeo]` `[Nueva Floresta]` `[Comuna 20 Centro]`
   - Con un toque el usuario queda ubicado de inmediato sin necesidad de escribir ni depender del satélite.
3. **Fallback elegante en registro y onboarding:**
   - Si la persona toca "Usar mi ubicación actual" al registrarse y no hay GPS disponible, en lugar de pintar una alerta roja del servidor, se abre un selector limpio con los barrios de la comuna para que toque el suyo y siga adelante sin frustración.

---

## 3. Principios de diseño para un frontend fluido y acogedor

No queremos meter librerías pesadas como Framer Motion o Three.js. Esas librerías aumentan el peso de la página en 100 KB o más y hacen que un celular sencillo (como un Redmi 9A o un Galaxy A03) se caliente o se sienta lento. El secreto de una app rápida y moderna está en CSS puro, microinteracciones táctiles y transiciones cortas.

### 1. Retroalimentación táctil inmediata (Active State)
En muchas páginas web tocas un botón y no sabes si reaccionó hasta que cambia la pantalla. En un celular, cada botón, tarjeta o chip debe sentirse vivo:
- En CSS: `active:scale-[0.98] transition-transform duration-100 ease-out`.
- Al poner el dedo encima se comprime apenas 2% y regresa de inmediato. Ese pequeño rebote le da al cerebro la confirmación física de que la orden fue recibida.

### 2. Transiciones de estado suaves (150 a 200 ms)
- Nada de cambios bruscos de color o bordes que parpadean.
- Al alternar filtros o seleccionar un oficio, el cambio de fondo crema a verde (`bg-brand-500 text-white`) debe tomar entre 150 y 200 ms con curva `ease-out`.
- En escritorio, un ligero realce vertical al pasar el ratón (`hover:-translate-y-0.5 hover:shadow-sm`) le quita lo plano a las tarjetas de negocios sin cansar la vista.

### 3. Esqueletos de carga con brillo suave (Skeleton Shimmer)
- En vez de pantallas en blanco o círculos de carga que dan la sensación de que la app se quedó pegada, mantenemos los esqueletos de tarjeta pero con un brillo degradado suave que pasa de izquierda a derecha.
- Da la sensación de que el contenido ya está por aparecer, bajando la percepción de espera en conexiones 3G/4G inestables.

### 4. Espaciado y confort visual (Paleta cálida)
- El fondo `#F9F8F2` (crema suave) ya evita el blanco encandilante de las pantallas baratas bajo el sol del mediodía.
- Mantener bordes redondeados consistentes (`rounded-xl` y `rounded-2xl`) y una tipografía clara, respetando los tamaños mínimos táctiles de 44 × 44 px para que un señor de 60 años con dedos gruesos no presione el botón equivocado.

---

## 4. Cómo suma esto a la rúbrica Fedesoft 2026

| Criterio | Puntos | Qué valor añade este trabajo |
|---|---|---|
| **Experiencia de usuario (UX)** | 10 pts | Resuelve la frustración del usuario cuando una función falla. El carrusel se puede explorar con facilidad y la ubicación nunca deja a nadie bloqueado con una pantalla de error. |
| **Diseño, presentación y calidad** | 15 pts | La aplicación deja de sentirse como un prototipo rígido y adquiere la fluidez de un producto comercial terminado, con microinteracciones agradables y consistentes. |
| **Inclusión y Bienestar** | Categoría | Permite que personas con poca experiencia tecnológica naveguen sin tropezar con errores técnicos incomprensibles de GPS o interfaces cortadas. |
| **Solución y resultados** | 15 pts | Demuestra pruebas en condiciones reales de campo (redes móviles, celulares de gama media y baja) y capacidad de corregir sobre la marcha con rigor técnico. |

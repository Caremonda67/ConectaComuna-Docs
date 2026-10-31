# Plan: alcance territorial y el rol del facilitador

Fecha: 29 de octubre de 2026. Faltan 15 días para la final.
Punto de partida: probando el flujo completo salió a la vista que la aplicación se comporta como si fuera de un solo barrio, aunque ahora busca en cualquier lugar de Colombia. El síntoma más claro lo señaló el dueño: a un facilitador le llegan reportes de negocios con los que no tiene nada que ver.

## El diagnóstico, que es mejor de lo que parecía

El modelo del facilitador **ya está bien pensado**. No hay que inventarlo de nuevo.

`facilitadores_negocio` vincula a una persona con **un negocio concreto**, y el vínculo nace del dueño: él genera un código de seis dígitos en su panel y se lo entrega a quien quiera que lo acompañe. El vínculo tiene estado (`pendiente`, `aprobado`, `rechazado`) y hay una restricción que impide duplicarlo. Es consentimiento explícito, persona a persona.

El sello territorial respeta ese vínculo al pie de la letra:

```sql
if not exists (
  select 1 from public.facilitadores_negocio fn
   where fn.negocio_id = p_negocio
     and fn.facilitador_id = auth.uid()
     and fn.estado_vinculacion = 'aprobado'
) then raise exception 'Solo un facilitador vinculado y aprobado puede otorgar el sello';
```

Un facilitador no puede sellar un negocio que no acompaña. Correcto.

**La moderación de reportes es la única pieza que rompe ese modelo.** La política de la base dice:

```sql
create policy reportes_select_facilitador on public.reportes_comunitarios
  for select using (
    auth.uid() = reportado_por_id
    or exists (select 1 from public.profiles
               where profiles.id = auth.uid()
                 and profiles.account_type = 'facilitador')
  );
```

Cualquier facilitador ve **todos** los reportes del país. Y la consulta del frontend (`listarReportes()`) tampoco filtra nada. Esa es la incoherencia: el resto del sistema delega confianza persona a persona, y la moderación la reparte a cualquiera con el rol.

Dicho de otro modo: **no hay que construir una jerarquía territorial, hay que hacer que la moderación obedezca la relación que ya existe.**

## Los sinsentidos concretos, con evidencia

### 1. Moderación sin relación con el negocio
Ya explicado. Un facilitador de Nueva Floresta ve el reporte de un taller de Bogotá, con el nombre de quien lo denunció.

### 2. Una sola persona puede marcar cualquier negocio
`reportes_comunitarios` no tiene restricción de unicidad sobre `(negocio_id, reportado_por_id)`. El umbral que deja un negocio "bajo observación" cuenta dos reportes pendientes, sin mirar quién los envió. Entonces una misma persona reporta dos veces al mismo negocio y lo deja marcado para toda la plataforma.

Es el tipo de cosa que un jurado técnico prueba en treinta segundos.

### 3. El territorio no existe como entidad
Las siete tablas son `profiles`, `businesses`, `orders`, `reviews`, `facilitadores_negocio`, `reportes_comunitarios` y `direcciones_usuario`. No hay comuna, ni barrio, ni zona.

`neighborhood` es texto libre en `profiles` y en `businesses`. "El Rodeo", "el rodeo" y "Barrio El Rodeo" son tres zonas distintas para el sistema. No se puede agrupar, ni contar, ni enrutar nada por territorio.

Lo único territorial que existe es un punto fijo (`COMUNA_CENTER`) y un radio de 35 km. Todo lo demás es distancia a ese punto.

### 4. El lenguaje quedó atrás del alcance
La aplicación busca en cualquier lugar de Colombia, pero sigue hablando de "la comuna" como si fuera un lugar único y conocido:

- La bandeja dice "queda marcado como bajo observación **para toda la comuna**". ¿Cuál comuna, si el negocio puede estar en Medellín?
- El asistente saluda con "tu Asistente Comunal para **toda nuestra comuna**".
- `PUNTO_PILOTO` se llama "Centro de la Comuna, Cali".
- La confirmación de un reporte dice que lo revisarán "los facilitadores **y la administración**", y el rol de administración ya se retiró.

No es cosmético: es lo que hace que la aplicación se sienta de un barrio.

---

## El plan

Tres bloques. El primero quita la incoherencia, el segundo le da sentido al rol, el tercero es para después de la final.

### Bloque 1 — Que la moderación obedezca el vínculo (1 día)

Un facilitador ve y resuelve **solo los reportes de los negocios que acompaña**.

En la base, cambiar la política:

```sql
create policy reportes_select_facilitador on public.reportes_comunitarios
  for select using (
    auth.uid() = reportado_por_id
    or exists (
      select 1 from public.facilitadores_negocio fn
       where fn.negocio_id = reportes_comunitarios.negocio_id
         and fn.facilitador_id = auth.uid()
         and fn.estado_vinculacion = 'aprobado'
    )
  );
```

Lo mismo para `update`. Y en el frontend, `listarReportes()` deja de pedir todo y pide lo de sus negocios.

**El hueco que esto abre, y cómo lo cerramos:** un negocio sin facilitador no tiene quién le revise los reportes. La respuesta honesta, y la que yo defendería ante el jurado:

> La señal es automática, la mediación es humana. Acumular reportes marca el negocio solo, sin que nadie intervenga. Que alguien revise y levante esa marca requiere un vecino que lo acompañe. Es un incentivo para apadrinar, no un vacío.

Y para que no sea un callejón sin salida, **el dueño ve los reportes de su propio negocio** y puede responder. Hoy no los ve: la política solo contempla a quien reporta y al facilitador. Eso es transparencia básica y además lo empuja a buscar un facilitador.

### Bloque 2 — Que un reporte sea de una persona (medio día)

```sql
alter table public.reportes_comunitarios
  add constraint un_reporte_por_persona unique (negocio_id, reportado_por_id);
```

Y el umbral pasa a contar **personas distintas**, que es lo que ya hace al tener la restricción. Dos vecinos distintos, no dos clics.

El mensaje al usuario también cambia: "ya reportaste este negocio, tu reporte está en revisión" en vez de aceptar el segundo en silencio.

### Bloque 3 — Que el territorio deje de ser un punto (1 a 2 días)

Lo mínimo para que la aplicación deje de sentirse de un barrio, sin inventar una tabla de comunas:

1. **Normalizar `neighborhood`.** Guardar siempre "Barrio, Ciudad" tal como lo devuelve el geocodificador, que ya es lo que hace el selector de ubicación. Así "El Rodeo, Cali" y "Chapinero, Bogotá" son comparables y agrupables.
2. **Guardar la ciudad aparte.** Una columna `ciudad` en `businesses`, llenada desde el geocodificador al crear o editar la ficha. Con eso se puede filtrar, contar y decir "3 talleres en Medellín" sin adivinar.
3. **Cambiar el lenguaje.** "La comuna" pasa a ser "tu zona" o el nombre real del lugar donde está la persona. El asistente ya sabe dónde está: que lo use. El mensaje de la bandeja dice "queda marcado para quienes lo busquen", no "para toda la comuna".
4. **Renombrar `PUNTO_PILOTO`** a algo que no mienta: es el centro del piloto en Cali, no "la comuna".

Esto no requiere tabla nueva ni migración de datos grande, y le quita a la aplicación el aire de maqueta de un solo barrio.

### Para después de la final

Una tabla de zonas con facilitadores asignados por territorio sería lo correcto a futuro: refleja cómo funciona de verdad una JAC, permite que alguien cubra un sector sin que cada dueño le dé un código, y abre la puerta a métricas por comuna. Pero es una decisión de arquitectura, no un parche de quince días. Documentarla como el siguiente paso es mejor que improvisarla ahora.

---

## Qué defender ante el jurado

El argumento que sale de esto es más fuerte que el que había:

> La confianza en ConectaComuna no la reparte un administrador: la delega el propio emprendedor, con un código que él genera y puede revocar. Quien lo acompaña puede sellar su negocio y responder por él ante la comunidad, y nadie más. La plataforma busca en toda Colombia, pero la responsabilidad siempre tiene nombre y apellido.

Eso explica por qué no hay un rol de administrador, que era un hueco, y lo convierte en una decisión de diseño.

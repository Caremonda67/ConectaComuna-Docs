# Tablas de la base de datos

Dejamos la base de datos con 7 tablas esenciales:

1. **profiles (Perfiles):** Guarda los datos básicos de todos (clientes, emprendedores y facilitadores) para usar una sola cuenta general.
2. **businesses(Negocios):** Guarda el perfil público del emprendimiento (nombre, ubicación, mapa, fotos y horarios).
3. **productos (Catálogo):** El menú o listado de servicios de cada negocio con su precio y si está activo.
4. **orders (Pedidos):** Controla las compras y en qué estado van (pendiente, en curso, finalizado).
5. **reviews (Reseñas):** Almacena las calificaciones de 1 a 5. Solo permite opinar si el pedido se cerró antes (anti-spam).
6. **facilitador_negocio (Apadrinamientos):** Tabla de permisos que vincula al dueño de un negocio con un familiar o líder que le administra la cuenta.
7. **direcciones_usuario (Mis ubicaciones):** Guarda las direcciones favoritas del cliente con latitud y longitud exacta (estilo Rappi) para los filtros del mapa.

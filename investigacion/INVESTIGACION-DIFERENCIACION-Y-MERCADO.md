# Investigación Estratégica: Diferenciación de ConectaComuna vs Marketplaces Tradicionales

Fecha: 25 de octubre de 2026  
Módulo: Estrategia de Mercado e Identidad de Producto  
Objetivo: Resolver la observación de semifinales de Fedesoft 2026 (Evaluador 2: *"herramienta muy normal, un marketplace"*)

---

## 1. Contexto Territorial y Datos de Mercado (Cali 2026)

Según las mediciones del DANE (GEIH 2025-2026) y reportes de la Cámara de Comercio de Cali:
- **Tasa de informalidad laboral en Cali:** oscila entre el 47% y 48% de la población ocupada.
- **Tejido productivo de barrio:** la mayoría de los ingresos en comunas populares se generan en microemprendimientos de subsistencia: costura casera, cerrajerías de garaje, barberías, venta ambulante, manicuristas y técnicos de barrio.
- **Brecha digital y adultos mayores:** más de 2.8 millones de personas en Colombia enfrentan serias dificultades con trámites digitales. En el comercio popular, los artesanos mayores de 50 años dependen de familiares jóvenes para cualquier gestión tecnológica.
- **Canales de cierre:** el 91.1% de los colombianos utiliza WhatsApp para coordinar compras y ventas, y herramientas como Nequi/Daviplata para transacciones entre personas, pero sin un marco de comprobantes oficiales que evite disputas.

---

## 2. Radiografía de Competidores y Fallas Críticas

### A. Marketplaces de Contratación Formal (Timbrit, Habitissimo, Klapp, Hogaru)
- **Falla de acceso:** exigen pagos de membresías, comisiones o cobro por cotizar (pay-per-lead). Un zapatero o modista no puede pagar $30.000 COP solo para ver los datos de un cliente potencial.
- **Falla de catálogo:** orientados a empresas o contratistas de remodelación pesada (plomería mayor, albañilería, arquitectura). No existe espacio para arreglos menores de ropa, cambio de cremalleras o venta de comidas caseras.
- **Falla geográfica:** operan a escala metropolitana, perdiendo la proximidad peatonal del barrio.

### B. Facebook Marketplace y Grupos Informales
- **Falla de seguridad y confianza:** Meta no valida identidades ni se hace responsable bajo la Ley del Consumidor (Ley 1480 de 2011). Abundan estafas con cheques sin fondos, anticipos robados y asaltos en entregas presenciales.
- **Falla de usabilidad:** el feed es caótico, ruidoso y saturado de publicaciones de otras ciudades y publicidad no solicitada.

---

## 3. La Tesis Central: Red Barrial de Confianza vs Marketplace Transaccional

Los marketplaces convencionales fracasan en los barrios porque intentan **cobrar comisiones e intermediar el dinero**.

ConectaComuna rompe este esquema mediante una postura clara:
1. **100% Gratuito y sin comisiones:** los ingresos van íntegros al artesano o microemprendedor.
2. **Trato Seguro Comunal:** regla comunitaria de máximo 50% de anticipo con código de comprobante oficial verificable y cláusula de mediación vecinal con la Junta de Acción Comunal (JAC).
3. **Inclusión Radical Dual:** 
   - **Copiloto por Voz con IA:** el adulto mayor habla naturalmente y la IA estructura su catálogo y tarifas con confirmación explícita.
   - **Facilitador Comunitario:** apadrinamiento joven con código de 6 letras para asistir en fotos y gestión.
4. **Venta al por Mayor Barrial:** encadenamientos productivos entre negocios del mismo sector (ej. confección de dotaciones escolares o compra de insumos locales).

---

## 4. Estrategia de Penetración Territorial Comunal

Para responder a la pregunta del jurado sobre cómo penetrar el mercado barrial:
1. **Alianzas con Juntas de Acción Comunal (JAC):** presentación en asambleas vecinales para validar los primeros 20 oficios por barrio.
2. **Volanteo estratégico en puntos de alta concurrencia:** panaderías, peluquerías, ferreterías y droguerías comunitarias.
3. **Campaña de apadrinamiento:** incentivar a jóvenes de colegios y universidades del sector a registrar a sus abuelos y vecinos como facilitadores comunitarios.


---

## 5. Implementación del Pilar 1: Pacto de Trato Seguro Verificable (Octubre 2026)

Con base en las observaciones del jurado de Fedesoft, se implementó y validó el primer pilar de blindaje barrial:

1. **Ruta Pública de Verificación sin Login (/trato-seguro/verificar/:codigo):**
   - Permite que cualquier vecino, cliente o líder de la Junta de Acción Comunal (JAC) escanee un código QR o ingrese un identificador (ej: CC-E9894D7D o CC-ORD-1) para corroborar la autenticidad del acuerdo en tiempo real.
   - Oculta datos sensibles respetando la privacidad (enmascaramiento del teléfono del cliente) mientras valida la identidad del taller.

2. **Blindaje Financiero del 50%:**
   - Muestra claramente el desglose: presupuesto total pactado, anticipo recibido/acordado (con porcentaje visible) y saldo protegido a pagar contra entrega a satisfacción.
   - Aplica la regla comunal: prohibido exigir más del 50% antes de iniciar el trabajo.

3. **Cláusula de Respaldo y Mediación Comunitaria de la JAC:**
   - Establece explícitamente el compromiso de mediación vecinal ante cualquier retraso o desacuerdo antes de recurrir a instancias externas.

4. **Generador y Verificador en el Asistente Comunal (Copiloto con IA):**
   - Cuando el usuario solicita un acuerdo en el chat o consulta por Trato Seguro, el Asistente genera una tarjeta interactiva con botón de acceso directo a la verificación pública y enlace oficial para compartir por WhatsApp.

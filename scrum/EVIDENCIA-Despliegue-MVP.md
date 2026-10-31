# Evidencia: despliegue del MVP en Vercel

**Fecha:** 14 de septiembre de 2026
**Fase:** inicio de la fase 3, pruebas piloto
**URL:** https://conecta-comuna-frontend.vercel.app/

## Qué pasó

El frontend quedó publicado en Vercel conectado al repositorio de GitHub, rama `main`. Cada push a esa rama dispara el build y el despliegue sin intervención.

## Decisiones

1. **El frontend habla con Supabase directamente.** Las tablas están protegidas con RLS, así que la llave anónima que viaja en el bundle no abre nada que la política no permita. El backend de Express queda para lo que necesita secreto: el asistente con Gemini y la verificación pública del pacto.
2. **El `vercel.json` llevaba una marca UTF-8 BOM** que hacía fallar el despliegue con "Invalid vercel.json file provided". Se limpió el archivo, se volvió a subir y el pipeline pasó.

## Lo que suma a la rúbrica

- **Uso de herramientas (15 pts):** CI/CD básico, variables de entorno separadas por entorno y solución de un fallo real de despliegue.
- **Solución y resultados (15 pts):** el proyecto deja de correr solo en un portátil y queda accesible para las pruebas con la comunidad.

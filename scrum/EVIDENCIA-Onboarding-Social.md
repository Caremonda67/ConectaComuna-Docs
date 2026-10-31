# Evidencia: Flujo de Onboarding para Auth Social y GPS
**Sprint 1: Autenticación y perfiles**
**Fecha:** 7 de septiembre de 2026

## ¿Qué hicimos hoy?
Decidimos que no queríamos perfiles incompletos cuando la gente usa Google o Facebook. En vez de dejarlos entrar a ciegas sin decirnos qué rol quieren ni dónde viven, montamos una pantalla obligatoria de \Onboarding\.

También implementamos algo muy útil: pedir la ubicación en vivo del usuario (GPS) para rellenar el nombre del barrio automáticamente. Esto cumple de lleno con la regla de oro: hacer la app súper fácil para gente sin experiencia técnica.

## Cambios realizados
1. **Onboarding obligatorio:** 
   Agregamos \onboarding_completado\ a la tabla de \profiles\ en Supabase.
   Ajustamos el layout de la app (\AppLayout.tsx\) para interceptar a cualquier usuario logueado que tenga este campo en falso, redirigiéndolo a \/onboarding\.
2. **Geolocalización en vivo:**
   Creamos el hook \useNeighborhoodLocator\ que usa \
avigator.geolocation\ y consulta la API de OpenStreetMap (Nominatim) para descubrir en qué barrio está la persona (funciona tanto para \OnboardingPage\ como para \RegisterPage\).
3. **Mejoras visuales y bugs:**
   Arreglamos un choque de Flexbox que descuadraba los botones sociales y reemplazamos los SVGs de Google y Facebook por etiquetas \<img />\ oficiales para evitar dolores de cabeza con diferentes navegadores.

## Resultado
Ahora el flujo es robusto y fluido. El usuario normal que usa correo no lo nota, pero el que usa Google entra rápido y el sistema lo ataja cordialmente para pedirle el teléfono, rol y ubicarlo.


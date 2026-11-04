# [ID: US-78] Resiliencia y degradación elegante ante intermitencias de servicios externos o VPN

- **Épica:** Sin Épica (Transversal - Operación e Interoperabilidad)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Sistema / Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** usuario del portal,  
> **quiero** que el sistema maneje de manera controlada cualquier timeout o interrupción temporal de conexión con bases o servicios externos, mostrando mensajes comprensibles sin bloquear la aplicación ni perder mis datos en curso,  
> **para** operar con confiabilidad y saber cómo proceder si un servicio externo se encuentra momentáneamente en mantenimiento.

## Reglas de Negocio y Conexiones Externas
- **Mitigación de Riesgos Técnicos (`ADR-026`, `ADR-028`):** Control de *timeouts* en el pool de conexiones de `node-oracledb`, manejo centralizado de errores HTTP en Express e interceptor de errores en Angular.

## Criterios de Aceptación
1. **Debe** configurar tiempos máximos de espera (*timeouts* de conexión y consulta) en los adaptadores hacia bases y servicios externos para evitar que peticiones colgadas saturen el servidor Node.js.
2. **Debe** capturar cualquier excepción de infraestructura en el backend y devolver respuestas de error estandarizadas y amigables en español, sin filtrar trazas de pila (*stack traces*) ni nombres de tablas internas al cliente.
3. **Debe** permitir que los módulos respaldados por PostgreSQL del portal sigan respondiendo aun si un servicio externo opcional presenta latencia elevada.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar simular la indisponibilidad temporal del túnel VPN hacia el laboratorio Oracle estando en modo `prod` y verificar que el servidor controla el timeout y responde de manera ordenada sin hacer caer el proceso de Node.js. *(Pasa)*
- **CP-02:** Probar provocar un error de red durante una consulta en el navegador y verificar que la interfaz muestra un aviso claro al usuario con opción de reintentar. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Gestión de pools de conexión, timeouts asíncronos y middleware global de manejo de excepciones. |
| **Esfuerzo** | Medio | Configuración de resiliencia en adaptadores de infraestructura e interceptores HTTP en Angular. |
| **Incertidumbre** | Baja | Probado frente a desconexiones de VPN del laboratorio. |

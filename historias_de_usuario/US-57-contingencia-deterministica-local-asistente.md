# [ID: US-57] Mecanismo de contingencia determinístico local ante falta de clave o caída del servicio LLM

- **Épica:** E11 - Asistente Virtual Docente con IA (Chatbot + LLM)
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Sistema / Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que utiliza el asistente virtual,  
> **quiero** que el chat continúe respondiendo mis consultas mediante el motor de reglas propio del portal aun cuando el proveedor externo de IA no esté disponible o no se haya configurado clave externa,  
> **para** que el asistente sea 100% operativo en cualquier entorno y nunca deje de responder por fallas de terceros.

## Reglas de Negocio y Conexiones Externas
- **Resiliencia Arquitectónica (`ADR-022`, `ADR-023`):** El asistente cuenta con un motor determinístico de intenciones y respuestas propio del dominio; cuando `GROQ_API_KEY` no está definida o la API externa falla/supera el timeout, el sistema conmuta de forma transparente al motor local sin arrojar errores al usuario.

## Criterios de Aceptación
1. **Debe** detectar de forma automática la ausencia de credenciales del proveedor LLM o cualquier error de red, timeout o límite de cuota (`HTTP 429 / 5xx`).
2. **Debe** procesar la consulta utilizando el clasificador determinístico local del módulo `asistente` y devolver la respuesta construida con los datos reales del docente sin interrumpir la experiencia de usuario.
3. **No debe** exponer trazas de error técnico ni fallar con `HTTP 500` frente al usuario cuando el servicio externo de IA no responda.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar enviar consultas al asistente en un entorno sin `GROQ_API_KEY` configurada y verificar que el motor determinístico responde con precisión sobre puntaje, legajo, Formulario C, alcances y vacantes. *(Pasa)*
- **CP-02:** Probar simular un fallo o timeout de conexión hacia la API externa de LLM y comprobar que el sistema aplica el fallback automático entregando la respuesta local en menos de 2 segundos. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Patrón *Circuit Breaker / Fallback* entre el adaptador LLM y el intérprete determinístico local. |
| **Esfuerzo** | Bajo | Manejo de excepciones y pruebas automatizadas de ambos modos de operación. |
| **Incertidumbre** | Nula | Garantiza disponibilidad total en demostraciones offline o sin internet externo. |

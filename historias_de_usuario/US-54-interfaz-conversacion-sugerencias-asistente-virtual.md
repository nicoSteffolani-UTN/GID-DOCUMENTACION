# [ID: US-54] Interfaz de conversación y sugerencias rápidas del Asistente Virtual Docente

- **Épica:** E11 - Asistente Virtual Docente con IA (Chatbot + LLM)
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** disponer de un asistente conversacional integrado en el portal con botones de consultas frecuentes y entrada de texto libre,  
> **para** resolver dudas sobre mi trámite anual, el Decreto 257/19, mi legajo o el uso del sistema de manera inmediata sin salir de la pantalla en la que estoy trabajando.

## Reglas de Negocio y Conexiones Externas
- **Arquitectura del Asistente (`ADR-022`):** Asistente propio del portal accesible desde cualquier sección del aspirante (`POST /api/asistente/consulta`), diseñado para guiar sobre el dominio de Junta Secundaria.

## Criterios de Aceptación
1. **Debe** proveer un componente de chat accesible en el portal para el rol `aspirante` que conserve el hilo de mensajes durante la sesión activa.
2. **Debe** ofrecer chips o botones de **«Sugerencias rápidas»** al abrir el asistente (ej.: *«¿Cuál es mi puntaje actual?»*, *«¿Qué escuelas me convienen para mi Formulario C?»*, *«¿Qué materias habilita mi título?»*, *«¿Cómo tengo mi legajo?»*, *«¿Qué cursos me suman más puntos?»*).
3. **Debe** permitir escribir preguntas en lenguaje natural libre y enviarlas presionando `Enter` o el botón de envío, mostrando un indicador visual mientras se procesa la respuesta.
4. **Debe** incluir enlaces de acción o navegación directa dentro de las respuestas cuando la consulta refiera a un módulo del portal (`/legajo`, `/formulario-c`, `/mapa`, `/puntaje`, `/simulador`).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar abrir el asistente virtual y presionar una de las sugerencias rápidas, verificando que se envía la consulta y se recibe una respuesta estructurada en el historial del chat. *(Pasa)*
- **CP-02:** Probar enviar un mensaje vacío o compuesto solo por espacios en blanco y verificar que el botón de envío permanece deshabilitado. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Gestión de estado conversacional en el cliente e integración con el endpoint `/api/asistente/consulta`. |
| **Esfuerzo** | Medio | Widget/vista de chat en Angular con soporte de formato enriquecido y sugerencias rápidas. |
| **Incertidumbre** | Nula | Implementado y documentado en ADR-022. |

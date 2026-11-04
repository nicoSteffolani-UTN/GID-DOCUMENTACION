# [ID: US-55] Respuestas contextualizadas con los datos reales del docente en el Asistente Virtual

- **Épica:** E11 - Asistente Virtual Docente con IA (Chatbot + LLM)
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que consulta al asistente virtual,  
> **quiero** que las respuestas utilicen el contexto real de mi cuenta (mi puntaje actual, el estado de mis antecedentes, mi antigüedad, mis alcances de título, mi Formulario C y las vacantes),  
> **para** recibir orientación personalizada sobre mi situación particular en lugar de respuestas genéricas estáticas.

## Reglas de Negocio y Conexiones Externas
- **Orquestación de Contexto (`ADR-022`, `ADR-023`):** El servicio del asistente en el backend reúne bajo demanda los datos del usuario autenticado desde los módulos de `legajo`, `puntaje`, `validador`, `formulario-c`, `mapa` y `capacitaciones` para fundamentar la respuesta en hechos verificables del sistema.

## Criterios de Aceptación
1. **Debe** responder consultas sobre el legajo informando la cantidad exacta de antecedentes validados, pendientes y rechazados del docente autenticado (y detallando los motivos de rechazo si los hubiera).
2. **Debe** responder consultas sobre puntaje y LOM informando el puntaje actual del docente desglosado según el Decreto 257/19.
3. **Debe** responder consultas sobre inscripción indicando qué escuelas y materias tiene actualmente en su Formulario C o cuáles se le sugieren como *Escuela Segura*, *Justa* y *Apuesta*.
4. **Debe** garantizar que el asistente consulte única y exclusivamente los datos pertenecientes al CUIL del token `JWT` de la sesión activa.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar preguntar al asistente *«¿Cómo está mi legajo y tengo algún antecedente rechazado?»* y verificar que la respuesta menciona los antecedentes reales del usuario autenticado. *(Pasa)*
- **CP-02:** Probar preguntar *«¿Cuál es mi puntaje y en qué escuelas estoy inscripto?»* y comprobar que los valores informados coinciden exactamente con los de `/puntaje` y `/formulario-c`. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Ensamblado de contexto transversal desde múltiples servicios de dominio del backend en tiempo de consulta. |
| **Esfuerzo** | Medio | Orquestador de intenciones y contexto en el módulo `asistente`. |
| **Incertidumbre** | Baja | Todos los servicios de dominio ya exponen sus contratos internos en el monolito modular. |

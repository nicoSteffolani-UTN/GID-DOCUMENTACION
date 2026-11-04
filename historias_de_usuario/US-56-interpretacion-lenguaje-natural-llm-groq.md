# [ID: US-56] Interpretación de lenguaje natural mediante modelo de lenguaje (LLM Groq) en el Asistente

- **Épica:** E11 - Asistente Virtual Docente con IA (Chatbot + LLM)
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** poder formular mis preguntas al asistente con mis propias palabras o expresiones coloquiales y que un modelo de lenguaje interprete mi intención sobre los datos oficiales del sistema,  
> **para** obtener respuestas claras, redactadas en lenguaje comprensible y fieles a las reglas del Decreto 257/19 sin depender de comandos rígidos.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa (`ADR-023`):** Integración con la API de inferencia de **Groq LLM** (`GROQ_API_KEY`) actuando como intérprete de texto libre y redactor acotado a los datos provistos por el sistema (evitando alucinaciones de puntajes o normativas).

## Criterios de Aceptación
1. **Debe** utilizar el modelo de lenguaje configurado (Groq) para clasificar la intención de consultas abiertas en lenguaje natural y redactar la respuesta apoyándose estrictamente en los datos del contexto del portal.
2. **No debe** inventar puntajes, resoluciones, escuelas ni vacantes que no estén presentes en el contexto oficial suministrado por el backend.
3. **Debe** mantener el foco temático dentro del ámbito de la Gestión Integral Docente y la normativa de Junta Secundaria de Córdoba, declinando amablemente solicitudes ajenas al propósito institucional del portal.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar realizar una pregunta coloquial (ej. *«Che, si hago un curso de 30 horas, ¿cuánto me suma para el año que viene?»*) y verificar que el asistente comprende la consulta y responde aplicando la regla `30 × 0,017 = 0,51 pts` del Decreto 257/19. *(Pasa)*
- **CP-02:** Probar realizar una consulta fuera del dominio educativo (ej. pedir una receta de cocina o código de programación ajeno) y verificar que el asistente redirige cortésmente la conversación hacia los trámites docentes del portal. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Diseño de *system prompt* con *grounding* estricto sobre datos del dominio e integración HTTP con Groq. |
| **Esfuerzo** | Medio | Adaptador LLM en el módulo `asistente` con control de timeouts y formato de salida. |
| **Incertidumbre** | Media | Dependencia de latencia y cuota de API externa (mitigada con fallback local en US-57). |

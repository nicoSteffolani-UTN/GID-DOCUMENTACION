# [ID: US-58] Resguardo de datos personales identificatorios (PII) en consultas al modelo de lenguaje externo

- **Épica:** E11 - Asistente Virtual Docente con IA (Chatbot + LLM)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 2 SP
- **Rol / Actor:** Sistema / Autoridad de Aplicación

## Descripción (Narrativa)
> **Como** responsable de seguridad de la información del sistema,  
> **quiero** que el conector hacia el modelo de lenguaje externo omita o anonimice datos filiatorios sensibles innecesarios (como CUIL completo, domicilio particular o tokens de sesión) al armar el prompt de consulta,  
> **para** preservar la privacidad de los docentes conforme a las buenas prácticas de protección de datos personales.

## Reglas de Negocio y Conexiones Externas
- **Política de Privacidad (`ADR-023`):** Al invocar la API externa de inferencia solo se envían los datos funcionales necesarios para elaborar la respuesta (ej. puntajes numéricos, categorías de alcance, nombres de materias/escuelas y estados de trámite), sin transmitir credenciales, tokens ni documentos PDF.

## Criterios de Aceptación
1. **No debe** incluir tokens `JWT`, cabeceras de autorización ni archivos binarios del legajo en el payload enviado al proveedor externo de LLM.
2. **Debe** limitar el contexto inyectado en el prompt exclusivamente a resúmenes funcionales estructurados (puntajes, rubros, materias y estados).
3. **Debe** sanitizar y acotar la longitud máxima del mensaje de entrada del usuario para prevenir abusos de payload.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar inspeccionar el payload armado por el conector hacia el servicio LLM y verificar que contiene únicamente el contexto funcional sin tokens de sesión ni credenciales. *(Pasa)*
- **CP-02:** Probar enviar un texto que supere el límite máximo de caracteres permitido en una consulta de chat y verificar que el backend lo rechaza con validación controlada (`HTTP 400`). *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Filtrado de campos en el DTO de contexto antes de serializar el prompt hacia el proveedor externo. |
| **Esfuerzo** | Bajo | Sanitización de entrada y proyección acotada de datos en el servicio del asistente. |
| **Incertidumbre** | Nula | Control total sobre el armado del prompt en el backend. |

# [ID: US-25] Advertencia preventiva al aspirante por selección de asignatura sin alcance de título

- **Épica:** E05 - Validador de Alcance de Títulos y Competencias
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** recibir una advertencia clara y explícita cuando seleccione en mi Formulario C una materia para la cual mis títulos registrados no figuran con alcance habilitante en el catálogo,  
> **para** decidir de manera informada si deseo reemplazarla por otra asignatura compatible o mantener la inscripción bajo revisión especial de la Junta.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio:** El sistema advierte de forma prominente la falta de alcance detectada en el catálogo, pero permite al docente continuar si decide declarar la inscripción bajo evaluación de la Junta (quedando el registro marcado para revisión del vocal).

## Criterios de Aceptación
1. **Debe** mostrar una alerta visual destacada cuando una materia seleccionada en el Formulario C no posea correspondencia de alcance (`Docente`, `Habilitante` ni `Supletorio`) con los títulos del legajo del aspirante.
2. **Debe** explicar en el mensaje de advertencia que la postulación en esa asignatura quedará identificada como **«Sin título habilitante en catálogo»** y estará sujeta a fiscalización por parte del vocal de la Junta.
3. **Debe** sugerir al docente revisar sus títulos cargados en `/legajo` o consultar las materias compatibles en el validador antes de confirmar definitivamente el Formulario C.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar una materia ajena al alcance del título del docente y verificar que aparece de inmediato la alerta preventiva advirtiendo la falta de alcance en catálogo. *(Pasa)*
- **CP-02:** Probar quitar esa materia sin alcance y reemplazarla por una materia con alcance `Docente`, comprobando que la advertencia desaparece. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Evaluación booleana del resultado del validador y presentación de alerta contextual. |
| **Esfuerzo** | Bajo | Componente de aviso en el formulario de inscripción y resumen previo al envío. |
| **Incertidumbre** | Nula | Regla implementada y trazada en `TRAZABILIDAD_SPRINT_P2.md` (actualización 07/10). |

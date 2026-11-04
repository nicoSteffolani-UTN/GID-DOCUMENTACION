# [ID: US-23] Validación en tiempo real de compatibilidad Título–Asignatura durante la selección del Formulario C

- **Épica:** E05 - Validador de Alcance de Títulos y Competencias
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que está confeccionando su Formulario C,  
> **quiero** que el sistema verifique automáticamente y en tiempo real si los títulos de mi legajo tienen alcance para cada materia o cargo que selecciono,  
> **para** recibir confirmación inmediata de la categoría legal en la que compito y evitar errores de inscripción involuntarios.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio:** Cruce automático entre los títulos registrados en el legajo del docente y el código de asignatura/cargo seleccionado en el Formulario C (`POST /api/validador/verificar` / `GET /api/validador/alcances`).

## Criterios de Aceptación
1. **Debe** ejecutar de forma automática la verificación de alcance cada vez que el docente agrega o selecciona una asignatura/cargo dentro del Formulario C.
2. **Debe** tomar el mejor alcance disponible cuando el docente posea más de un título en su legajo (priorizando `Docente` sobre `Habilitante`, y `Habilitante` sobre `Supletorio`).
3. **Debe** exhibir en la fila de la materia seleccionada una insignia clara con el resultado: `Alcance Docente (18 pts)`, `Alcance Habilitante (12 pts)`, `Alcance Supletorio (6 pts)` o `Sin alcance habilitante`.
4. **Debe** responder en menos de 2 segundos para no interrumpir el flujo interactivo de carga de la inscripción.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar una asignatura cubierta por el título del docente en el Formulario C y verificar que se muestra de inmediato la etiqueta de alcance correspondiente (`Docente`, `Habilitante` o `Supletorio`). *(Pasa)*
- **CP-02:** Probar con un docente que posee dos títulos (uno `Supletorio` y otro `Docente` para la misma materia) y comprobar que el validador aplica la categoría más favorable (`Docente`). *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Cruce reactivo entre el legajo del usuario y el catálogo de alcances de la escuela/materia. |
| **Esfuerzo** | Medio | Integración entre el servicio de validador y el formulario dinámico de inscripción. |
| **Incertidumbre** | Baja | Contrato de validación definido en ADR-009. |

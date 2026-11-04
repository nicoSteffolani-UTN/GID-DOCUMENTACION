# [ID: US-26] Marcado automático de «Sin título habilitante» para revisión del Vocal de Junta

- **Épica:** E05 - Validador de Alcance de Títulos y Competencias
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Vocal de Junta / Sistema

## Descripción (Narrativa)
> **Como** vocal evaluador de la Junta de Clasificación,  
> **quiero** que el sistema marque automáticamente con la etiqueta «Sin título habilitante» aquellas inscripciones donde el aspirante confirmó materias no cubiertas por el alcance de su título en el padrón,  
> **para** identificar de un vistazo los casos que requieren auditoría normativa prioritaria en la bandeja de fiscalización por escuela.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio:** Cuando un aspirante confirma su Formulario C incluyendo una o más materias sin alcance registrado para su título, el backend persiste el indicador de alerta para que el vocal de la escuela correspondiente lo visualice resaltado en `/junta` (`US-04`, `US-06`, actualización del 07/10).

## Criterios de Aceptación
1. **Debe** registrar en los metadatos de la inscripción del Formulario C un indicador cuando al menos una de las materias seleccionadas no posea alcance habilitante según el validador.
2. **Debe** exhibir la insignia de advertencia **«Sin título habilitante»** junto al aspirante en el listado de revisión del vocal (`/junta`) y en el detalle de su legajo/inscripción.
3. **Debe** detallar dentro de la vista del vocal exactamente cuál o cuáles de las materias inscriptas carecen de alcance automático frente al título declarado.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar confirmar un Formulario C con una materia sin alcance como aspirante y luego ingresar como vocal de esa escuela en `/junta`, verificando que el aspirante aparece marcado con la etiqueta «Sin título habilitante». *(Pasa)*
- **CP-02:** Probar confirmar un Formulario C donde todas las materias tienen alcance `Docente` o `Habilitante` y comprobar que en la vista del vocal no se asigna la alerta de falta de título habilitante. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Propagación del flag de validación de alcance desde la confirmación del Formulario C hacia el DTO de la bandeja de Junta. |
| **Esfuerzo** | Bajo | Persistencia del atributo en la inscripción y badge visual en la bandeja del vocal. |
| **Incertidumbre** | Nula | Flujo implementado e integrado entre los módulos `formulario-c`, `validador` y `junta`. |

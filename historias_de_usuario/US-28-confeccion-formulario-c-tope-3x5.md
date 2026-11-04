# [ID: US-28] Confección del Formulario C con control estricto de tope reglamentario (3 escuelas × 5 materias/cargos)

- **Épica:** E06 - Convocatoria Anual e Inscripción en Formulario C
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** armar mi inscripción anual en el Formulario C seleccionando hasta un máximo de 3 establecimientos educativos y hasta 5 asignaturas o cargos en cada uno,  
> **para** cumplir estrictamente con los topes reglamentarios vigentes de la Junta de Clasificación Secundaria sin riesgo de que mi postulación sea impugnada por exceso de cupo.

## Reglas de Negocio y Conexiones Externas
- **Regla Reglamentaria Central:** Tope máximo estricto de **3 escuelas estatales** por ciclo lectivo y hasta **5 espacios curriculares (materias/cargos)** por cada escuela seleccionada (máximo 15 postulaciones en total).
- **Supresión de Escuela Base (ADR-013):** Las 3 escuelas son de libre elección dentro del padrón provincial, sin exigir la fijación de una «escuela base».

## Criterios de Aceptación
1. **Debe** permitir al docente agregar entre **1 y 3 establecimientos educativos** distintos en su Formulario C del ciclo lectivo vigente.
2. **Debe** bloquear en la interfaz y rechazar en el backend (`HTTP 400`) cualquier intento de agregar un cuarto establecimiento educativo.
3. **Debe** permitir seleccionar entre **1 y 5 asignaturas o cargos** distintos dentro de cada establecimiento elegido.
4. **Debe** bloquear en la interfaz y rechazar en el backend (`HTTP 400`) cualquier intento de agregar una sexta asignatura en una misma escuela.
5. **Debe** impedir que se seleccione dos veces la misma escuela o dos veces la misma asignatura dentro de la misma escuela.
6. **Debe** exigir que toda escuela agregada al Formulario C tenga al menos 1 materia/cargo seleccionado antes de permitir guardar o confirmar.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar 3 escuelas diferentes con 5 materias válidas en cada una y verificar que el sistema acepta la configuración sin errores. *(Pasa)*
- **CP-02:** Probar intentar agregar una cuarta escuela al Formulario C y verificar que el botón se deshabilita y el backend rechaza la petición si se envía por API. *(Falla controlada)*
- **CP-03:** Probar intentar seleccionar una sexta materia dentro de una misma escuela y verificar el bloqueo por tope reglamentario de 5 asignaturas. *(Falla controlada)*
- **CP-04:** Probar intentar guardar una escuela con 0 materias seleccionadas y comprobar que el sistema exige elegir al menos un espacio curricular. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Validaciones invariantes de dominio en cliente y servidor sobre estructuras anidadas (Escuelas → Materias). |
| **Esfuerzo** | Alto | Interfaz interactiva de armado de Formulario C, contadores de cupo disponible y validaciones de API. |
| **Incertidumbre** | Nula | Regla de negocio taxativa (3x5) y decisión arquitectónica ADR-013. |

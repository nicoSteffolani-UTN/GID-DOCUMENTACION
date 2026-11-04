# [ID: US-47] Postulación voluntaria a solicitud de cobertura de vacante activa

- **Épica:** E09 - Alertas y Cobertura de Vacantes Activas
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante con legajo apto y título habilitante,  
> **quiero** registrar mi postulación voluntaria a una solicitud de cobertura de vacante activa dentro del plazo de recepción,  
> **para** participar en el orden de mérito de adjudicación del cargo u horas cátedra con mi puntaje oficial de la LOM vigente.

## Reglas de Negocio y Conexiones Externas
- **Reglas de Validación de Postulación (Instancia 2 §19.11):**
  1. La convocatoria de la vacante debe encontrarse abierta (fecha/hora del servidor anterior al cierre).
  2. El docente debe poseer alcance habilitante para la materia y su inscripción del ciclo vigente en regla.
  3. No se admiten postulaciones duplicadas del mismo docente para el mismo identificador de vacante.

## Criterios de Aceptación
1. **Debe** habilitar el botón de confirmación de postulación cuando la vacante se encuentre activa y el docente cumpla con las condiciones de habilitación.
2. **Debe** validar en el backend que el usuario posea su inscripción anual en regla y alcance de título para la asignatura solicitada.
3. **Debe** asociar automáticamente al registro de la postulación el puntaje de la LOM vigente que le corresponde al docente para esa asignatura.
4. **Debe** impedir el procesamiento de la postulación si la fecha y hora actual del servidor superan el límite de cierre de la solicitud de cobertura.
5. **Debe** bloquear cualquier intento de registrar una segunda postulación idéntica para el mismo cargo y escuela por parte del mismo CUIL.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar postularse a una vacante activa cumpliendo los requisitos de Formulario C y alcance de título, verificando que la postulación queda registrada exitosamente. *(Pasa)*
- **CP-02:** Probar intentar postularse a una vacante cuyo período de recepción ya se encuentra cerrado y verificar que el sistema rechaza la operación. *(Falla controlada)*
- **CP-03:** Probar intentar enviar una segunda postulación para la misma vacante y verificar el bloqueo por duplicidad. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Validaciones transaccionales de vigencia temporal, alcance de título, estado de Formulario C y unicidad por CUIL–Vacante. |
| **Esfuerzo** | Medio | Endpoint transaccional de postulación y flujo de confirmación en UI. |
| **Incertidumbre** | Baja | Reglas especificadas en el backlog de Instancia 2 (§19.11). |

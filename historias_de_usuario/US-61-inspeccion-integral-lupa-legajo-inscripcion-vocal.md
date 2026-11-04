# [ID: US-61] Inspección integral (Lupa) del legajo e inscripción del aspirante por parte del Vocal

- **Épica:** E12 - Gestión y Fiscalización para Vocales de Junta
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal evaluador de la Junta,  
> **quiero** abrir la vista detallada («Lupa») de un aspirante para inspeccionar todos sus antecedentes cargados, sus certificados PDF adjuntos, sus materias inscriptas en el Formulario C y su puntaje resultante,  
> **para** contar con todos los elementos de juicio necesarios antes de aceptar o rechazar cada antecedente.

## Reglas de Negocio y Conexiones Externas
- **Endpoint Asociado:** `GET /api/junta/aspirantes/:cuil/legajo?escuelaId=`.

## Criterios de Aceptación
1. **Debe** desplegar la vista detallada del aspirante mostrando sus datos identificatorios (Apellido, Nombre, CUIL, título principal), las materias en las que se inscribió en esa escuela (indicando su categoría de alcance o la alerta «Sin título habilitante») y el listado completo de antecedentes de su legajo.
2. **Debe** mostrar para cada antecedente del legajo: categoría, denominación, institución emisora, fecha de emisión, horas reloj, origen (`manual` / `provincia`), acceso al documento PDF probatorio y estado actual (`Pendiente`, `Validado`, `Rechazado`).
3. **Debe** exhibir el motivo de rechazo y la fecha de dictamen en aquellos antecedentes que ya hayan sido observados previamente.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar hacer clic en «Revisar legajo / Lupa» sobre un aspirante de la escuela y verificar que se visualizan sus materias inscriptas y todos sus antecedentes con sus estados y archivos adjuntos. *(Pasa)*
- **CP-02:** Probar consultar el legajo de un aspirante mediante un `escuelaId` ajeno al vocal autenticado y verificar que el servidor bloquea el acceso (HTTP 403). *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Agregación de datos de identidad, inscripción en Formulario C, validación de alcances y antecedentes del legajo para la vista del vocal. |
| **Esfuerzo** | Medio | Panel de detalle/lupa de aspirante en `/junta` y endpoint seguro en backend. |
| **Incertidumbre** | Nula | Contrato definido en la matriz US → API de `TRAZABILIDAD_SPRINT_P2.md`. |

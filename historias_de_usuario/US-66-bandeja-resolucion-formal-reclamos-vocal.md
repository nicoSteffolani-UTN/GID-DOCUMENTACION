# [ID: US-66] Bandeja de resolución formal de reclamos para Vocales de la Junta

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal de la Junta de Clasificación,  
> **quiero** gestionar desde una bandeja centralizada los reclamos interpuestos por los docentes de mis escuelas asignadas y emitir una resolución formal fundamentada («Favorable» o «Desestimado»),  
> **para** rectificar el puntaje antes de la publicación de la LOM Definitiva o dejar asentado el dictamen de desestimación.

## Reglas de Negocio y Conexiones Externas
- **Reglas de Resolución de Reclamos (Instancia 2 §19.5):**
  1. Solo accesible para el rol `vocal` sobre su ámbito jurisdiccional.
  2. La resolución exige una fundamentación escrita obligatoria (mínimo 20 caracteres).
  3. Una vez emitido el dictamen (`Favorable` o `Desestimado`), la resolución queda firme y bloqueada contra ediciones posteriores.

## Criterios de Aceptación
1. **Debe** listar en la bandeja del vocal los reclamos abiertos ordenados cronológicamente por fecha de ingreso.
2. **Debe** presentar en una misma vista el descargo del docente, el antecedente o rubro cuestionado y el archivo PDF adjunto.
3. **Debe** exigir una fundamentación obligatoria de al menos **20 caracteres** para registrar el dictamen (`Favorable` o `Desestimado`).
4. **Debe** habilitar la actualización del estado del antecedente / recálculo del puntaje cuando el reclamo se resuelva como `Favorable`.
5. **Debe** bloquear de forma definitiva la edición del reclamo una vez emitido y confirmado el dictamen final.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar resolver un reclamo como `Favorable` ingresando una fundamentación válida de más de 20 caracteres y verificar que el reclamo pasa a estado cerrado/resuelto y habilita la rectificación del antecedente. *(Pasa)*
- **CP-02:** Probar intentar cerrar un reclamo con el campo de resolución vacío o con menos de 20 caracteres y verificar que el sistema impide el guardado. *(Falla controlada)*
- **CP-03:** Probar intentar modificar un reclamo que ya fue resuelto previamente y comprobar que se encuentra en modo de solo lectura. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Transición de estados de reclamo con impacto condicional sobre el legajo y puntaje del aspirante. |
| **Esfuerzo** | Medio | Bandeja de resolución de reclamos para el rol vocal y validaciones de inmutabilidad post-dictamen. |
| **Incertidumbre** | Baja | Especificado en detalle en Instancia 2 (§19.5). |

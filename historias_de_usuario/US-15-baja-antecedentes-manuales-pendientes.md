# [ID: US-15] Baja o eliminación de antecedentes cargados manualmente en estado Pendiente

- **Épica:** E03 - Gestión del Legajo Digital y Antecedentes
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** poder eliminar un antecedente que cargué manualmente con algún error mientras aún se encuentre en estado `Pendiente`,  
> **para** corregir mi legajo y volver a subir el certificado correcto antes de que un vocal de la Junta emita su dictamen o se cierre la convocatoria.

## Reglas de Negocio y Conexiones Externas
- **Regla de Integridad del Legajo:** Solo pueden darse de baja antecedentes cargados por el usuario que aún no hayan sido dictaminados como `Validado` definitivos, y siempre que la ventana de modificación del ciclo lectivo lo permita (`DELETE /api/legajo/antecedentes/:id`).

## Criterios de Aceptación
1. **Debe** habilitar la acción **«Eliminar»** sobre los antecedentes cargados manualmente que se encuentren en estado `Pendiente` (o para subsanar un ítem rechazado cargando uno nuevo).
2. **Debe** solicitar una confirmación explícita al usuario antes de proceder con la eliminación del antecedente.
3. **Debe** impedir la eliminación de antecedentes que ya posean dictamen **`Validado`** por la Junta de Clasificación o que provengan del padrón oficial inmutable.
4. **Debe** remover el antecedente del listado activo del legajo y reflejar el cambio de inmediato en los contadores y en el cálculo preliminar de puntaje.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar eliminar un antecedente cargado manualmente que está en estado `Pendiente`, confirmar el diálogo y verificar que desaparece del listado del legajo. *(Pasa)*
- **CP-02:** Probar intentar eliminar mediante llamada directa a la API (`DELETE /api/legajo/antecedentes/:id`) un antecedente perteneciente a otro CUIL y verificar el bloqueo HTTP 403/404. *(Falla controlada)*
- **CP-03:** Probar cancelar el diálogo de confirmación de borrado y comprobar que el antecedente permanece intacto en el legajo. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Operación de baja controlada por estado y pertenencia en base de datos. |
| **Esfuerzo** | Bajo | Endpoint `DELETE` con validaciones de dominio y diálogo de confirmación en UI. |
| **Incertidumbre** | Nula | Reglas de transición de estados de antecedente completamente definidas. |

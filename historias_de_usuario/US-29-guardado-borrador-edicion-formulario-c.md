# [ID: US-29] Guardado en borrador y edición del Formulario C durante la ventana de convocatoria

- **Épica:** E06 - Convocatoria Anual e Inscripción en Formulario C
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** guardar mi selección de escuelas y materias en estado de borrador o modificar mi inscripción mientras la ventana anual de convocatoria se encuentre abierta,  
> **para** poder revisar mi estrategia con el mapa y las ayudas de decisión en distintas sesiones antes del cierre definitivo del padrón.

## Reglas de Negocio y Conexiones Externas
- **Máquina de Estados de Inscripción:** `Borrador` (en edición por el aspirante) → `Confirmado` (presentación formal registrada).
- **Persistencia:** Base de datos PostgreSQL del portal (`formulario_c_usuario`, ADR-028).

## Criterios de Aceptación
1. **Debe** permitir guardar el progreso del Formulario C en estado **`Borrador`** (`PUT /api/formulario-c/borrador` / guardado parcial) sin exigir aún el cierre definitivo.
2. **Debe** recuperar automáticamente la selección guardada cuando el docente cierre sesión y vuelva a ingresar a `/formulario-c` en otro momento.
3. **Debe** permitir agregar, quitar o reemplazar escuelas y materias libremente mientras la ventana de convocatoria anual (`INSCRIPCION_ABIERTA`) esté activa.
4. **Debe** mostrar de forma visible el estado actual del formulario (`Sin iniciar`, `Borrador guardado` o `Confirmado`) junto con la fecha y hora de la última modificación.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar 2 escuelas con sus materias, guardar como borrador, cerrar sesión, volver a ingresar y verificar que las 2 escuelas y sus materias siguen cargadas en `/formulario-c`. *(Pasa)*
- **CP-02:** Probar reemplazar una de las escuelas del borrador por otra escuela del padrón y guardar los cambios verificando la actualización correcta. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Persistencia de estado del formulario por CUIL y ciclo lectivo en PostgreSQL. |
| **Esfuerzo** | Medio | Endpoints de lectura/escritura de borrador y manejo de estado reactivo en Angular. |
| **Incertidumbre** | Nula | Esquema de persistencia definido en ADR-028. |

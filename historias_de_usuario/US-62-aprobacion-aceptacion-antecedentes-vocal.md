# [ID: US-62] Aprobación (Aceptación) de antecedentes del legajo por el Vocal de Junta

- **Épica:** E12 - Gestión y Fiscalización para Vocales de Junta
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal evaluador de la Junta,  
> **quiero** aprobar con un clic aquellos antecedentes del aspirante cuya documentación probatoria cumpla con todos los requisitos normativos del Decreto 257/19,  
> **para** cambiar su estado a «Validado» e incorporar su puntaje a la calificación oficial del docente.

## Reglas de Negocio y Conexiones Externas
- **Transición de Estado:** `Pendiente` → `Validado` (`POST /api/junta/aspirantes/:cuil/antecedentes/:id/aceptar` con body `{ escuelaId }`).
- **Impacto Directo:** Al pasar a `Validado`, el antecedente se computa de inmediato en el motor de valoración reglamentaria del docente y actualiza el estado del aspirante en la escuela (pasando a `Sin Pendiente` cuando ya no quedan ítems por revisar).

## Criterios de Aceptación
1. **Debe** habilitar el botón **«Aceptar / Validar»** en cada antecedente que se encuentre en estado `Pendiente` dentro de la vista de revisión del vocal.
2. **Debe** actualizar en la base de datos el estado del antecedente a **`Validado`**, registrando la fecha/hora del dictamen y el identificador del vocal interviniente.
3. **Debe** recalcular automáticamente el estado general del aspirante en la bandeja de la escuela (`Sin Pendiente` si era el último ítem pendiente) y actualizar su puntaje validado.
4. **Debe** reflejar de forma inmediata el nuevo estado `Validado` en la vista `/legajo` del propio docente aspirante.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar aceptar el único antecedente `Pendiente` de un aspirante desde `/junta` y verificar que el ítem cambia a `Validado` y el estado general del aspirante pasa a `Sin Pendiente`. *(Pasa)*
- **CP-02:** Probar ingresar luego con la cuenta del aspirante a `/legajo` y `/puntaje` y verificar que el antecedente figura `Validado` y suma los puntos correspondientes. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Actualización transaccional de estado en PostgreSQL, espejo en tablas de Junta y recálculo de puntaje. |
| **Esfuerzo** | Bajo | Endpoint `POST .../aceptar` y actualización reactiva en la UI de `/junta`. |
| **Incertidumbre** | Nula | Flujo central del MVP verificado con pruebas de integración y E2E. |

# [ID: US-32] Bloqueo de alta y modificación del Formulario C fuera de la ventana de convocatoria

- **Épica:** E06 - Convocatoria Anual e Inscripción en Formulario C
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 2 SP
- **Rol / Actor:** Sistema / Docente Aspirante

## Descripción (Narrativa)
> **Como** autoridad de la Junta de Clasificación Secundaria,  
> **quiero** que el sistema bloquee automáticamente cualquier nueva inscripción o modificación del Formulario C cuando la ventana anual de convocatoria se encuentre cerrada,  
> **para** garantizar la igualdad de condiciones entre todos los aspirantes y asegurar la inmutabilidad del padrón durante el período de valoración y confección de la LOM.

## Reglas de Negocio y Conexiones Externas
- **Regla de Calendario Oficial:** El parámetro de configuración `INSCRIPCION_ABIERTA` (y fechas de apertura/cierre de convocatoria) gobierna la habilitación de escritura sobre el módulo de Formulario C (Diagrama 13 de estados de inscripción anual).

## Criterios de Aceptación
1. **Debe** verificar en el backend el estado de la ventana de convocatoria (`INSCRIPCION_ABIERTA`) antes de procesar cualquier petición `POST` o `PUT` sobre `/api/formulario-c`.
2. **Debe** rechazar con código `HTTP 403 / 409` y mensaje explicativo cualquier intento de crear o modificar una inscripción cuando la convocatoria esté cerrada.
3. **Debe** mostrar el Formulario C en **modo exclusivo de solo lectura** en la interfaz cuando la convocatoria haya finalizado, permitiendo al docente consultar su inscripción realizada y descargar su comprobante PDF.
4. **Debe** informar en un cartel visible las fechas oficiales de cierre de la convocatoria y la etapa actual del proceso (ej. «Período de inscripción finalizado — En etapa de confección de LOM»).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `/formulario-c` con el parámetro de convocatoria en estado cerrado (`INSCRIPCION_ABIERTA = false`) y verificar que los selectores de escuelas y materias están bloqueados en modo lectura y solo se permite consultar/descargar el comprobante. *(Pasa)*
- **CP-02:** Probar enviar una petición `POST /api/formulario-c` directamente a la API estando la convocatoria cerrada y verificar que el servidor rechaza la operación sin alterar la base de datos. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Verificación de estado de configuración en middleware/servicio y deshabilitación del formulario en UI. |
| **Esfuerzo** | Bajo | Guard de estado de convocatoria en backend y vista de solo lectura en Angular. |
| **Incertidumbre** | Nula | Regla de negocio documentada en el modelo de estados de inscripción. |

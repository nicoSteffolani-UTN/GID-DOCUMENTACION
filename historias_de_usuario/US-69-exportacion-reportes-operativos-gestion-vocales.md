# [ID: US-69] Exportación de reportes operativos de gestión para Vocales de la Junta

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P3 (Baja / Evolución)
- **Estimación:** 3 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal de la Junta de Clasificación Secundaria,  
> **quiero** exportar reportes consolidados de la actividad de revisión de mis escuelas asignadas (cantidad de legajos evaluados, pendientes, validados y observados),  
> **para** contar con información operativa para el seguimiento del ciclo anual de valoración.

## Reglas de Negocio y Conexiones Externas
- **Control de Acceso (Instancia 2 §19.21):** Funcionalidad restringida exclusivamente al perfil `vocal` sobre las escuelas de su ámbito asignado.

## Criterios de Aceptación
1. **Debe** permitir al vocal generar y descargar un reporte de estado de revisión filtrando por escuela asignada y por estado de legajo (`Todos`, `Pendiente`, `Sin Pendiente`, `Observado`).
2. **Debe** incluir en el reporte los totales de aspirantes inscriptos, cantidad de antecedentes validados, rechazados y pendientes por establecimiento, junto con la fecha y hora de generación.
3. **Debe** informar con un mensaje claro cuando los filtros seleccionados no arrojen registros para exportar, sin generar archivos vacíos corruptos.
4. **Debe** denegar con `HTTP 403` cualquier intento de invocación por parte de un usuario con rol `aspirante`.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar generar el reporte operativo desde una cuenta con rol `vocal` seleccionando una escuela con aspirantes y verificar la descarga del archivo con los totales actualizados. *(Pasa)*
- **CP-02:** Probar intentar acceder al endpoint de exportación de reportes de Junta con un token de rol `aspirante` y verificar el rechazo HTTP 403. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consolidación de métricas de la bandeja de Junta y exportación tabular/PDF. |
| **Esfuerzo** | Bajo | Acción de exportación en `/junta` y formateo de datos. |
| **Incertidumbre** | Nula | Datos ya calculados en el servicio del módulo `junta`. |

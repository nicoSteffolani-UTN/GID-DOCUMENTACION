# [ID: US-48] Emisión de comprobante digital inmutable de postulación a vacante

- **Épica:** E09 - Alertas y Cobertura de Vacantes Activas
- **Prioridad:** P2 (Media)
- **Estimación:** 2 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que registró una postulación o manifestación de interés a una vacante,  
> **quiero** obtener un comprobante digital en formato PDF con los datos del cargo, mi puntaje computado y el sello temporal de presentación,  
> **para** contar con una constancia fehaciente de mi participación en la convocatoria de cobertura.

## Reglas de Negocio y Conexiones Externas
- **Trazabilidad Documental:** El comprobante refleja los datos exactos al momento de la postulación (CUIL, escuela, materia, puntaje aplicado y fecha/hora del servidor).

## Criterios de Aceptación
1. **Debe** emitir y permitir descargar un comprobante en formato PDF tras el registro exitoso de la postulación a la vacante.
2. **Debe** consignar en el comprobante: número único de trámite, CUIL y nombre del docente, datos del establecimiento educativo, asignatura/cargo, carácter y carga horaria de la vacante, puntaje LOM adjuntado y fecha/hora oficial de registro.
3. **Debe** permitir volver a descargar el comprobante desde el historial de postulaciones del docente en cualquier momento.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar finalizar una postulación a vacante y verificar la generación y descarga inmediata del comprobante PDF con todos los datos reglamentarios. *(Pasa)*
- **CP-02:** Probar acceder posteriormente al listado de postulaciones realizadas y volver a descargar el mismo comprobante verificando que mantiene su código y marca temporal original. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Generación de reporte PDF plano a partir del registro inmutable de postulación. |
| **Esfuerzo** | Bajo | Reutilización del servicio generador de comprobantes PDF del portal. |
| **Incertidumbre** | Nula | Estructura análoga al comprobante de inscripción del Formulario C. |

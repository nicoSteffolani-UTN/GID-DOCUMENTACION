# [ID: US-46] Consulta del detalle analítico de una solicitud de cobertura de vacante

- **Épica:** E09 - Alertas y Cobertura de Vacantes Activas
- **Prioridad:** P2 (Media)
- **Estimación:** 2 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** consultar la ficha analítica completa de una vacante publicada (incluyendo horarios de dictado, curso/división, motivo/carácter de la vacante y requisitos de alcance),  
> **para** verificar mi disponibilidad horaria y compatibilidad antes de postularme o manifestar interés.

## Reglas de Negocio y Conexiones Externas
- **Fuente de Datos:** Publicaciones de vacantes de la Junta de Clasificación (`VT_PUBLI_EMPRE_MAT_ASPIRANTES` / detalle de vacante).

## Criterios de Aceptación
1. **Debe** mostrar en el detalle de la vacante seleccionada: nombre y código de la escuela estatal, departamento y localidad, domicilio, asignatura o cargo, curso y división, turno, carga horaria total y desglose de horarios/módulos si están informados.
2. **Debe** indicar el carácter del cargo (`Interinato` o `Suplencia`) y la fecha y hora exacta de cierre de la convocatoria.
3. **Debe** informar en la misma vista el alcance que posee el título del docente para esa asignatura y su puntaje LOM aplicable en dicho establecimiento.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar abrir el detalle de una vacante activa y verificar que se visualizan todos los atributos del cargo, la escuela y la compatibilidad del docente. *(Pasa)*
- **CP-02:** Probar visualizar una vacante cuya fecha de cierre ya expiró y verificar que se muestra claramente el estado «Convocatoria Cerrada». *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Presentación detallada del objeto de publicación de vacante enriquecido con alcance y puntaje del docente. |
| **Esfuerzo** | Bajo | Modal o vista de detalle de vacante en Angular. |
| **Incertidumbre** | Nula | Atributos provistos por la consulta de publicaciones de la Junta. |

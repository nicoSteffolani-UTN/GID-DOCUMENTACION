# [ID: US-44] Bandeja de notificaciones y alertas de nuevas vacantes compatibles con el perfil docente

- **Épica:** E09 - Alertas y Cobertura de Vacantes Activas
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** contar con una bandeja de notificaciones en el portal que me alerte sobre nuevas solicitudes de cobertura de vacantes en las escuelas de mi Formulario C, de mi lista de interés o compatibles con mi título,  
> **para** enterarme a tiempo de las oportunidades de designación antes de que venza el plazo de recepción de postulaciones.

## Reglas de Negocio y Conexiones Externas
- **Integración:** Módulo de notificaciones de vacantes (`ADR-020`, `/notificaciones`, `GET /api/notificaciones`) alimentado por las publicaciones de `VT_PUBLI_EMPRE_MAT_ASPIRANTES`.

## Criterios de Aceptación
1. **Debe** mostrar en la barra superior del portal un indicador de notificaciones con el contador de alertas de vacantes no leídas.
2. **Debe** listar en la bandeja de notificaciones las alertas de vacantes activas detallando: establecimiento, asignatura/cargo, carácter (interinato/suplencia), carga horaria, turno y fecha/hora límite de cierre de recepción.
3. **Debe** permitir marcar notificaciones individuales o todas como leídas.
4. **Debe** ofrecer desde cada alerta un enlace directo para inspeccionar la escuela en el mapa o acceder al detalle de la vacante.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar abrir la bandeja de notificaciones con un docente que posee alertas de vacantes en sus materias habilitadas y verificar que se muestran los datos del cargo y la fecha de cierre. *(Pasa)*
- **CP-02:** Probar marcar una notificación como leída y verificar que disminuye el contador de alertas pendientes en el encabezado. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Cruce de publicaciones de vacantes con el perfil/alcances e intereses del docente autenticado (ADR-020). |
| **Esfuerzo** | Medio | Módulo backend `notificaciones` y componente de bandeja de alertas en Angular. |
| **Incertidumbre** | Baja | Estructura de publicaciones de vacantes definida en las vistas de la Junta. |

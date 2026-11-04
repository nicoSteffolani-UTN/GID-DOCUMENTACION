# [ID: US-45] Filtrado de alertas de vacantes por escuelas de mi Formulario C, intereses y departamento

- **Épica:** E09 - Alertas y Cobertura de Vacantes Activas
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** filtrar las alertas de vacantes activas para ver exclusivamente las correspondientes a las 3 escuelas de mi Formulario C, las de mi lista de interés o las de mi departamento,  
> **para** priorizar las convocatorias donde ya estoy inscripto en LOM o que se encuentran en mi zona de preferencia.

## Reglas de Negocio y Conexiones Externas
- **Regla de Priorización:** Un docente tiene prioridad reglamentaria en aquellas escuelas dondeefectivizó su inscripción anual en el Formulario C; no obstante, también puede consultar llamados abiertos o de sus escuelas de interés.

## Criterios de Aceptación
1. **Debe** permitir filtrar el listado de alertas de vacantes por: **«Escuelas de mi Formulario C»**, **«Mis escuelas de interés»**, **«Compatibles con mi título»** y **«Por Departamento»**.
2. **Debe** destacar con una etiqueta especial aquellas vacantes que pertenezcan a una de las 3 escuelas donde el docente tiene su Formulario C confirmado.
3. **Debe** ocultar por defecto las convocatorias cuyo plazo de recepción ya haya vencido, permitiendo consultarlas opcionalmente bajo un filtro de «Cerradas / Históricas».

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar activar el filtro «Escuelas de mi Formulario C» y verificar que solo se listan las vacantes pertenecientes a los establecimientos donde el docente está inscripto. *(Pasa)*
- **CP-02:** Probar activar el filtro «Mis escuelas de interés» y comprobar que se muestran las vacantes de las escuelas marcadas previamente en el mapa. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Filtrado cruzado entre el listado de vacantes y los IDs de escuelas del Formulario C e intereses del usuario. |
| **Esfuerzo** | Bajo | Filtros rápidos por pestañas/chips en la vista de notificaciones y vacantes. |
| **Incertidumbre** | Nula | Datos ya disponibles en el contexto del usuario autenticado. |

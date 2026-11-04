# [ID: US-59] Bandeja de establecimientos asignados al Vocal con aislamiento estricto de jurisdicción

- **Épica:** E12 - Gestión y Fiscalización para Vocales de Junta
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal evaluador de la Junta de Clasificación Secundaria,  
> **quiero** visualizar al ingresar a mi bandeja de trabajo exclusivamente los establecimientos educativos asignados a mi ámbito de fiscalización junto con sus indicadores de aspirantes pendientes,  
> **para** organizar mi labor diaria de revisión respetando la distribución jurisdiccional de la Junta.

## Reglas de Negocio y Conexiones Externas
- **Regla de Aislamiento Jurisdiccional (`ADR-014`, `ADR-018`, `US-06`):** Cada vocal tiene asignado un conjunto determinado de establecimientos del padrón; un vocal **no puede** listar ni dictaminar sobre escuelas o aspirantes asignados exclusivamente a otro vocal (`GET /api/junta/escuelas`).

## Criterios de Aceptación
1. **Debe** listar en `/junta` (`GET /api/junta/escuelas`) únicamente las escuelas asignadas al CUIL del vocal autenticado.
2. **Debe** mostrar para cada escuela de la bandeja: código identificador, nombre oficial, departamento, localidad, cantidad total de aspirantes inscriptos y **cantidad de aspirantes con antecedentes pendientes de revisión**.
3. **Debe** permitir buscar o filtrar escuelas dentro de la cartera asignada al vocal por nombre, código o departamento, y ordenarlas priorizando aquellas que tienen mayor cantidad de revisiones pendientes.
4. **Debe** devolver `HTTP 403 / 404` si un vocal intenta consultar por API una escuela que no pertenece a su asignación (ej. Vocal 1 intentando acceder a `ESC-003` asignada al Vocal 2).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar con el vocal `27999888777` y verificar que visualiza sus escuelas asignadas (ej. `ESC-001`, `ESC-002` y su cartera del padrón) sin ver `ESC-003`. *(Pasa)*
- **CP-02:** Probar ingresar con el vocal `27111222333` y verificar que visualiza `ESC-003` y no accede a las escuelas del primer vocal. *(Pasa)*
- **CP-03:** Probar invocar `GET /api/junta/escuelas/ESC-003/aspirantes` con el token del vocal `27999888777` y verificar que el servidor rechaza la petición por aislamiento de jurisdicción. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Particionamiento de escuelas por vocal y control de autorización a nivel de recurso (RBAC + pertenencia de escuela). |
| **Esfuerzo** | Medio | Servicio de asignación vocal–escuela en backend y bandeja principal de `/junta` en Angular. |
| **Incertidumbre** | Baja | Implementado y validado en los casos de prueba de aislamiento entre vocales. |

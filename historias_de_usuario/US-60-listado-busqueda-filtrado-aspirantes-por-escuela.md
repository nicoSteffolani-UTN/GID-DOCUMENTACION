# [ID: US-60] Listado, búsqueda y filtrado de aspirantes por escuela y estado de revisión

- **Épica:** E12 - Gestión y Fiscalización para Vocales de Junta
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal de la Junta de Clasificación,  
> **quiero** seleccionar una escuela de mi bandeja y listar a todos los docentes aspirantes vinculados a ella filtrando por su estado de revisión (`Pendiente`, `Sin Pendiente` u `Observado`) o buscando por CUIL y apellido,  
> **para** localizar con rapidez los legajos que requieren dictamen.

## Reglas de Negocio y Conexiones Externas
- **Estados de Revisión del Aspirante en la Bandeja:**
  - `Pendiente`: El aspirante posee al menos un antecedente en estado `Pendiente` de evaluación.
  - `Observado`: El aspirante posee antecedentes `Rechazados` y ninguno pendiente.
  - `Sin Pendiente` / `Al día`: Todos los antecedentes presentados por el aspirante ya fueron `Validados`.

## Criterios de Aceptación
1. **Debe** listar los aspirantes del establecimiento seleccionado (`GET /api/junta/escuelas/:escuelaId/aspirantes?estado=`) mostrando: Apellido y Nombre, CUIL, título principal, estado general de su legajo (`Pendiente`, `Sin Pendiente`, `Observado`), cantidad de ítems pendientes y alertas normativas (como «Sin título habilitante»).
2. **Debe** permitir filtrar la tabla por estado de revisión (`Todos`, `Pendiente`, `Sin Pendiente`, `Observado`).
3. **Debe** permitir buscar aspirantes dentro del listado por número de CUIL parcial/completo o por apellido y nombre.
4. **Debe** actualizar en tiempo real el estado del aspirante en la fila de la escuela luego de que el vocal termine de evaluar sus antecedentes.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar una escuela en `/junta` y aplicar el filtro `Pendiente`, verificando que solo se listan los aspirantes con certificados pendientes de dictamen. *(Pasa)*
- **CP-02:** Probar buscar un aspirante por su número de CUIL en el buscador de la escuela y verificar que se filtra la fila correspondiente. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consolidación dinámica entre el padrón de la escuela, los Formularios C confirmados y el estado actual del legajo de cada aspirante. |
| **Esfuerzo** | Medio | Endpoint parametrizado en `/api/junta` y tabla con filtros reactivos en Angular. |
| **Incertidumbre** | Nula | Implementado y verificado en las suites de prueba de `junta`. |

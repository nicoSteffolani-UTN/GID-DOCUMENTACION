# [ID: US-12] Sincronización automática de antecedentes y cursos desde registros provinciales (SGE / Red Provincial)

- **Épica:** E03 - Gestión del Legajo Digital y Antecedentes
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** sincronizar con un clic los antecedentes y capacitaciones que ya figuran registrados a mi nombre en las bases oficiales de la Provincia (SGE / Red Provincial de Formación Docente),  
> **para** incorporarlos de manera inmediata a mi legajo digital sin tener que cargarlos manualmente ni escanear certificados emitidos por el propio Ministerio.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Padrón de antecedentes provinciales por CUIL (`POST /api/legajo/sincronizar` / adaptador SGE / fixture `antecedentes-provincia.mock.json`).
- **Regla de Negocio:** La operación es **idempotente** (ejecutarla múltiples veces no duplica los cursos ya importados en el legajo del docente).

## Criterios de Aceptación
1. **Debe** ofrecer un botón de acción **«Sincronizar con SGE / Provincia»** en la vista de Mi Legajo (`/legajo`).
2. **Debe** consultar por el CUIL del docente autenticado los antecedentes y cursos oficiales disponibles en la fuente provincial.
3. **Debe** incorporar al legajo del docente únicamente aquellos registros provinciales que aún no hayan sido importados previamente (idempotencia por identificador de origen).
4. **Debe** marcar los antecedentes incorporados por esta vía con el origen `provincia` para distinguirlos visualmente de los cargados manualmente.
5. **Debe** informar al finalizar la operación la cantidad de nuevos antecedentes agregados (o indicar «Tu legajo ya se encuentra al día con los registros provinciales» si no hay novedades).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar presionar «Sincronizar con SGE / Provincia» por primera vez con un docente que posee antecedentes en el padrón provincial y verificar que se incorporan al listado del legajo indicando la cantidad sumada. *(Pasa)*
- **CP-02:** Probar presionar nuevamente el botón «Sincronizar» de forma consecutiva y comprobar que **no se duplican** los antecedentes ya importados (0 nuevos agregados). *(Pasa)*
- **CP-03:** Probar sincronizar con un CUIL que no registra cursos previos en la fuente provincial y verificar que el sistema responde con un mensaje informativo sin arrojar error. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Lógica de conciliación e idempotencia para evitar duplicados por clave externa (`origenId`). |
| **Esfuerzo** | Medio | Endpoint de sincronización en backend, adaptador de fuente provincial y feedback de estado en UI. |
| **Incertidumbre** | Media | Disponibilidad de servicios web externos de SGE (mitigado mediante puerto y adaptador en el backend). |

# [ID: US-73] Consulta y filtrado del historial de auditoría de accesos y cambios

- **Épica:** E14 - Gobernanza, Seguridad y Auditoría Inmutable
- **Prioridad:** P2 (Media / Implementada)
- **Estimación:** 5 SP
- **Rol / Actor:** Administrador de la Junta / Usuario de Sistemas (`ADR-038`, `ADR-041`)

## Descripción (Narrativa)
> **Como** Administrador de la Junta / Usuario de Sistemas,  
> **quiero** consultar y filtrar los registros de auditoría de accesos y cambios operativos por rango de fechas, usuario o categoría de evento,  
> **para** fiscalizar la secuencia cronológica de eventos ante consultas administrativas, reclamos o revisiones de gestión.

## Reglas de Negocio y Conexiones Externas
- **Pantalla y Endpoint de Consulta (`ADR-038`, `ADR-041`):** Pantalla `Junta → Auditoría` (`junta-auditoria.component`) consumiendo `GET /api/junta/administracion/auditoria`.
- **Control de Acceso:** Acceso exclusivo para CUILs configurados en `ADMINISTRADORES_CUIL`. Vocales estándar y docentes reciben HTTP 403.
- **Inmutabilidad (`ADR-032`):** Solo lectura (*append-only* en PostgreSQL sobre `auditoria_acceso` y `auditoria_cambio`).

## Criterios de Aceptación
1. **Debe** exponer la pantalla `Junta → Auditoría` en modo exclusivo de **solo lectura** sobre los registros consolidados de accesos y cambios.
2. **Debe** permitir filtrar los eventos por: Usuario (búsqueda por nombre o CUIL), Tipo de evento / Categoría (accesos, ingresos, ingresos rechazados, salidas, cambios, legajo, Formulario C, Junta, validaciones, administración) y Rango de fechas (`desde` / `hasta`).
3. **Debe** presentar los resultados ordenados cronológicamente desde el más reciente al más antiguo y paginados de a 50 registros por página.
4. **Debe** exhibir el detalle analítico legible de la operación, usuario, rol e IP de origen sin exponer contraseñas ni secretos.
5. **Debe** denegar con `HTTP 403 Forbidden` cualquier intento de acceso a la pantalla o endpoint por parte de usuarios sin rol de Administrador de la Junta.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `Junta → Auditoría` con un usuario con rol de Administrador de la Junta y verificar el despliegue del listado cronológico de accesos y cambios. *(Pasa)*
- **CP-02:** Probar filtrar eventos por rango de fechas y categoría de validaciones de antecedentes y verificar la paginación correcta de a 50 registros. *(Pasa)*
- **CP-03:** Probar acceder a la pantalla o endpoint de auditoría con un usuario con rol docente o vocal estándar y verificar el bloqueo de seguridad con HTTP 403. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consultas indexadas sobre PostgreSQL con filtros opcionales combinados y control de acceso RBAC. |
| **Esfuerzo** | Medio | Pantalla `junta-auditoria` y servicio `ConsultaAuditoriaService` en el módulo de administración (`ADR-041`). |
| **Incertidumbre** | Nula | Implementado y verificado en `/src/server/src/modules/administracion`. |

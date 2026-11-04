# [ID: US-73] Consulta y filtrado del historial de auditoría de accesos y cambios

- **Épica:** E14 - Gobernanza, Seguridad y Auditoría Inmutable
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Vocal de Junta / Autoridad de Auditoría

## Descripción (Narrativa)
> **Como** vocal o personal autorizado de supervisión,  
> **quiero** consultar y filtrar los registros de auditoría de accesos y cambios operativos por rango de fechas, tipo de operación, entidad o CUIL involucrado,  
> **para** verificar la secuencia cronológica de eventos ante consultas administrativas o revisiones de gestión.

## Reglas de Negocio y Conexiones Externas
- **Endpoints de Consulta (`ADR-032`):** `GET /api/auditoria/accesos` y `GET /api/auditoria/cambios`, protegidos por control de rol autorizado.

## Criterios de Aceptación
1. **Debe** exponer endpoints y vista de consulta en modo exclusivo de **solo lectura** sobre los registros de `auditoria_acceso` y `auditoria_cambio`.
2. **Debe** permitir filtrar los eventos por: número de CUIL, tipo de acción/evento, entidad afectada y rango de fechas (`desde` / `hasta`), ordenados desde el evento más reciente al más antiguo.
3. **Debe** paginar o limitar la cantidad de registros devueltos por consulta para asegurar un rendimiento óptimo de la base de datos.
4. **Debe** denegar con `HTTP 403` cualquier intento de consulta a los listados generales de auditoría por parte de usuarios sin el rol autorizado.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar consultar el historial de cambios filtrando por el CUIL de un aspirante específico con un perfil autorizado y verificar que se listan en orden cronológico sus cargas de legajo, confirmación de Formulario C y dictámenes recibidos. *(Pasa)*
- **CP-02:** Probar invocar el endpoint general de auditoría con un token de rol `aspirante` estándar y verificar que el servidor rechaza la petición con HTTP 403. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consultas indexadas sobre PostgreSQL con filtros opcionales combinados y control de acceso por rol. |
| **Esfuerzo** | Medio | Endpoints en el módulo `auditoria` y vista tabular de consulta. |
| **Incertidumbre** | Nula | Implementado en `/src/server/src/modules/auditoria`. |

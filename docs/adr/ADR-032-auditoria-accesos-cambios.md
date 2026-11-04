# ADR-032: Auditoría de accesos y cambios

**Estado:** Aceptado  
**Fecha:** 2026-10-21  
**Relacionado:** ADR-028 (PostgreSQL), ADR-031 (ingreso con CiDi)

## Contexto

US-20 pide un registro inmutable de los accesos al portal y US-21, la auditoría de los cambios sobre el legajo. La revisión del vocal y el Formulario C también modifican datos del aspirante.

## Decisión

1. Dos tablas en PostgreSQL (migración `003_auditoria.sql`):
   - `auditoria_acceso`: ingreso, ingreso rechazado y salida, con medio (`mock` o `cidi`), CUIL, rol, IP y motivo del rechazo.
   - `auditoria_cambio`: cada cambio aceptado en el legajo, el Formulario C y la revisión de la Junta, con quién lo hizo, la ruta de la API (sin valores), el aspirante afectado, el antecedente y el código HTTP.
2. Las tablas solo admiten agregar filas: un disparador rechaza `UPDATE`, `DELETE` y `TRUNCATE`.
3. Nunca se guardan contraseñas, tokens, el cuerpo de los pedidos ni el contenido de los PDF.
4. Los cambios se registran con un middleware por módulo, después de que la API respondió sin error. Los accesos, desde el controlador de ingreso.
5. Las escrituras van en cola y no frenan la respuesta. Si una falla, se informa en el log del servidor y se sigue con las demás.
6. Sin PostgreSQL (desarrollo con archivo y tests), el registro queda en memoria.

## Consecuencias

- La consulta de la auditoría se hace con SQL sobre esas tablas; no hay pantalla en el portal.
- Corregir un registro implica agregar otro: el historial no se reescribe.

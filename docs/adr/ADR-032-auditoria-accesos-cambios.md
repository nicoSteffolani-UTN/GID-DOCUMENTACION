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

## Addendum 2026-11-13: detalle legible de cada cambio

La historia se reescribió como «Registrar accesos y cambios (auditoría técnica)» (US-20 + US-21): sin pantalla, la consulta la DBA o soporte del Ministerio. Pide usuario, fecha, hora y **detalle** de la operación, y que el registro se entienda aunque el antecedente se elimine después. Con solo la ruta y el id, un antecedente borrado ya no decía qué era.

1. La migración `015_auditoria_detalle.sql` agrega `detalle TEXT NULL` a `auditoria_cambio`. Los disparadores de la 004 no cambian: `UPDATE`, `DELETE` y `TRUNCATE` siguen rechazados.
2. Cada ruta arma el texto en el momento del cambio y lo deja con `detalleAuditoria(res, texto)` (`res.locals.auditoriaDetalle`). El middleware lo guarda junto con el registro, solo si la respuesta salió bien. Las rutas que no lo informan guardan `null`, como antes.
3. Textos cortos (hasta 300 caracteres) con rubro, nombre del antecedente, horas y qué campos cambiaron, nunca sus valores:
   - Legajo: «Alta: formación pos-inicial "Didáctica de la Matemática", 40 h», «Baja: títulos "Profesorado de Historia"» (se describe antes de borrar), «Trajo de la provincia: 3 antecedentes».
   - Reclamos del docente: «Reclamo: antecedente "…"», «Retiró el reclamo: …», «Vio las resoluciones de sus reclamos».
   - Inscripción: «Inscripción: 2 escuelas, 6 materias o cargos».
   - Junta: «Aceptó: …», «Rechazó: … (motivo 28)», «Corrigió: … (campos: Promedio, Plan de estudio)», «Valoró título: Matemática (materia), caso …», «Confirmó la valoración sugerida del título: …», «Cargó antigüedad: 2 filas», «Resolvió el reclamo (aprobado): …», «Períodos: inscripción abierto, reclamos del … al …».
4. No van el contenido de los PDF, el CUIL de terceros (el aspirante ya está en `aspirante_cuil`), el texto libre del motivo ni valores sensibles: del concepto profesional se guarda el año, no la calificación.
5. Sin PostgreSQL (`PERSISTENCIA=archivo` y tests) el detalle queda en el registro en memoria, igual que el resto de la auditoría.

**Pruebas.** `detalle.test.ts` recorre la API (alta, baja que conserva el nombre después de borrar, inscripción, corrección y rechazo del vocal). Además corre las migraciones 003, 004 y 014 en un PostgreSQL real en memoria (PGlite, dependencia de desarrollo) para comprobar que el detalle se guarda y que `UPDATE` y `DELETE` siguen fallando. pg-mem no ejecuta disparadores, y PGlite corre en un proceso aparte (`__tests__/pglite-runner.cjs`) porque la VM de Jest no permite el `import()` dinámico que hace al cargar.

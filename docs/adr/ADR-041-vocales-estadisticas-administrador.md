# ADR-041: Alta y baja de vocales, estadísticas y auditoría del administrador

**Estado:** Aceptado
**Fecha:** 2026-11-14
**Relacionado:** ADR-018 (reparto de escuelas base), ADR-028 (persistencia), ADR-032 (auditoría de accesos y cambios), ADR-038 (administrador de la Junta), relevamiento en la Junta del 2026-11-13

## Contexto

Con ADR-038 el administrador de la Junta abre y cierra los períodos y mueve escuelas base entre vocales. Pero quiénes son vocales sigue saliendo de fuera del portal: la tabla `T_USUARIOS_JUNTA`, la variable `VOCALES_CUIL` o los usuarios de ejemplo. Si un vocal se va en plena convocatoria, hay que pedirle a quien administra la base o el servidor que lo saque; si se suma uno nuevo, lo mismo.

En el relevamiento en la Junta se pidió además ver lo que suma cada vocal: cuántos antecedentes revisó, corrigió o cargó y cuántos reclamos resolvió.

La historia «Consultar historial de auditoría» (Instancia 2) pide que un administrador vea los eventos registrados, filtrados por usuario, fechas y tipo de evento, sin poder modificarlos. ADR-032 guarda los registros; faltaba la pantalla.

## Decisión

### Alta y baja de vocales

1. La pantalla **Junta → Vocales** (solo para administradores) lista los vocales activos, con su origen (la Junta o un alta en el portal) y sus escuelas, y los dados de baja.
2. **Alta:** el administrador carga CUIL y nombre. La persona entra al portal como vocal desde su próximo ingreso y suma al reparto automático. Si ya entró alguna vez, vale el nombre de su cuenta.
3. **Baja:** pide un motivo (de 10 a 300 caracteres). En el momento:
   - el vocal deja de entrar a la Junta, aunque tenga la sesión abierta (la API responde 403);
   - en su próximo ingreso entra como aspirante;
   - sus escuelas, incluidas las que le habían pasado a mano, vuelven al reparto entre los demás.
4. No se puede dar de baja al propio administrador, a otro administrador ni al último vocal.
5. **Reactivar** devuelve el rol de vocal y lo suma otra vez al reparto.
6. Lo que decide el administrador manda sobre la tabla de la Junta, `VOCALES_CUIL` y los usuarios de ejemplo. Se guarda una fila por persona, identificada por DNI como en la Junta, con quién lo decidió y cuándo (migración `018_vocal_portal.sql`).

### Estadísticas por vocal

7. La pantalla **Junta → Estadísticas** (solo para administradores) muestra, por vocal:
   - antecedentes aceptados, rechazados y cargados en nombre del docente;
   - correcciones de oficio y por reclamo;
   - valoraciones de títulos;
   - reclamos aprobados y rechazados;
   - legajos trabajados (aspirantes distintos) y última actividad;
   - escuelas, aspirantes y antecedentes pendientes de sus escuelas.
8. Las acciones se acotan por fecha (día de Córdoba), con atajos para los últimos 7 días y el mes. Escuelas, aspirantes y pendientes son siempre a la fecha.
9. No hay registro nuevo: se cuenta con lo que el portal ya guarda con quién y cuándo (la revisión de cada antecedente, las correcciones, las cargas del vocal, las valoraciones y la resolución de los reclamos). Cuenta la última decisión sobre cada antecedente.
10. Un vocal que ya no está pero hizo algo en el período figura como inactivo.
11. Se descarga en Excel con el mismo formato que los reportes del vocal (US-24).

### Consulta de la auditoría

12. La pantalla **Junta → Auditoría** (solo para administradores) lista los accesos y los cambios del más nuevo al más viejo, con quién, su rol, qué hizo y el detalle legible cuando la ruta lo dejó (addendum de ADR-032).
13. Filtra por usuario (nombre o CUIL), rango de fechas (día de Córdoba) y tipo de evento: accesos, ingresos, ingresos rechazados, salidas, cambios, legajo, Formulario C, Junta, validaciones (aceptar y rechazar) o administración (períodos, vocales y escuelas).
14. Pagina de a 50 registros. Desde la interfaz no se pueden modificar ni borrar.
15. Las altas, bajas, transferencias e intercambios del administrador dejan su detalle en la auditoría (quién, a quién), sin el motivo de la baja (texto libre).

## Consecuencias

- La Junta suma y saca vocales sin depender de quien administra la base o el servidor. Para nombrar a un administrador sigue haciendo falta el `.env` (ADR-038).
- Si un vocal dado de baja sigue figurando en `T_USUARIOS_JUNTA`, el portal lo mantiene afuera hasta que el administrador lo reactive.
- Como las estadísticas cuentan la última decisión, si un vocal acepta un antecedente que otro había rechazado, el rechazo deja de contar para el primero.
- La consulta de la auditoría no abre los registros a quien no es administrador: sigue siendo un audit trail de solo agregar (ADR-032).

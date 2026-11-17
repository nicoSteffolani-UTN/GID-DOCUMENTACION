# ADR-040: Historial de revisión del legajo

**Estado:** Aceptado
**Fecha:** 2026-11-13
**Relacionado:** historia «Registrar validaciones y rechazos» (US-22), ADR-033 (valoración del título), ADR-034 (corrección de antecedentes), ADR-035 (reclamos), ADR-032 (auditoría)

## Contexto

La historia pide «consultar posteriormente el historial de decisiones tomadas». Las decisiones del vocal ya se guardaban, pero repartidas y no todas:

| Decisión | Dónde estaba | ¿Quedaba con fecha y vocal? |
|---|---|---|
| Rechazo | `historialObservaciones` del antecedente | Sí, todos, con motivo |
| Corrección | `correcciones` del antecedente | Sí, todas |
| Resolución de reclamo | `resolucion` del reclamo | Sí |
| Aceptación | `revision` del antecedente | Solo la última, con el CUIL del vocal y sin su nombre |
| Valoración del título | `valoracionesTitulo` | Solo la vigente: un cambio pisa la anterior y quitarla la borra |
| Antigüedad de la Junta | antecedentes de origen `JUNTA` | Solo la última carga |
| Devolver a revisión (reclamo aprobado) | — | No: limpia `revision` |

Si un antecedente se aceptaba, se rechazaba y se volvía a aceptar, la primera aceptación se perdía. `auditoria_cambio` (ADR-032) tiene todo, pero solo en PostgreSQL, con el CUIL del vocal, y es una auditoría técnica sin pantalla.

## Decisión

1. **Registro de eventos por aspirante, solo agregar.** Cada decisión del vocal (aceptar, rechazar, corregir, devolver a revisión, valorar el título, confirmar la sugerencia, cargar la antigüedad y resolver un reclamo) agrega un `EventoRevision`: fecha, vocal (CUIL y nombre), acción, antecedente, cómo se llamaba en ese momento (`sobre`), motivo y comentario. Va en la sección `revisionByCuil` del store y se persiste como lo demás: memoria, archivo (`dev-store.json`) o la tabla `revision_evento` (migración `015`). La API nunca reescribe ni borra un evento: en PostgreSQL solo se insertan los nuevos.
2. **Por aspirante, no dentro del antecedente.** Los antecedentes se eliminan (el docente) o se reemplazan (la antigüedad de la Junta), y hay decisiones que no son de un antecedente (valoración, reclamo de valoración).
3. **Compatible hacia atrás, sin migrar datos.** Lo decidido antes de este registro se reconstruye de las estructuras de siempre (rechazos, correcciones, última aceptación, valoraciones vigentes, antigüedad y reclamos) y se junta con los eventos, sin repetir (misma acción, fecha y antecedente o materia). Antes de cada decisión nueva, lo reconstruido que todavía no está en el registro se guarda como evento, porque esa decisión puede pisarlo (por ejemplo, la aceptación que solo vivía en `revision`). Así, el historial anterior queda fijo desde la primera decisión nueva sobre el aspirante.
4. **Formato existente intacto.** `historialObservaciones`, `correcciones`, `revision`, las valoraciones y los reclamos se siguen guardando igual.
5. **Consulta solo para el vocal.** `GET /api/junta/aspirantes/:cuil/historial-revision?escuelaId=` con la misma regla de acceso que la revisión del legajo (vocal con la escuela base del aspirante asignada). Devuelve los eventos del más nuevo al más viejo, con el nombre del vocal y nunca su CUIL.
6. **Pantalla.** En la revisión del legajo, el botón «Historial» de la barra abre la sección «Historial de revisión», de solo lectura, con un filtro por antecedente o materia. El docente no la ve: ya ve sus observaciones y correcciones en el legajo.

## Alternativas descartadas

- **Leer `auditoria_cambio`:** solo existe con PostgreSQL, guarda la ruta técnica y el CUIL, y es para soporte (ADR-032).
- **Una lista de eventos dentro de cada antecedente:** se pierde al eliminarlo o al reemplazar la antigüedad, y no cubre la valoración del título ni los reclamos.
- **Migrar los datos viejos a la tabla nueva:** la reconstrucción da el mismo resultado sin un script que correr en cada base.

## Consecuencias

- La aceptación de antes de este cambio aparece solo si era la última decisión sobre el antecedente; las aceptaciones anteriores a esa ya se habían perdido.
- Una aceptación reconstruida muestra el nombre con que el vocal entró al portal, o «Vocal de la Junta» si no se conoce (`revision` guardaba solo el CUIL).
- El registro crece con cada decisión; para una convocatoria son decenas de eventos por aspirante.

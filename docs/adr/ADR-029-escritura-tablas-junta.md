# ADR-029: Escritura en las tablas de la Junta en desarrollo y prueba

**Estado:** Aceptado  
**Fecha:** 2026-10-21  
**Relacionado:** ADR-003 (Oracle), ADR-018 (vocal × escuela), ADR-019 (convocatoria), ADR-028 (PostgreSQL)

## Contexto

Lo que carga el aspirante y valida el vocal se guarda en PostgreSQL (ADR-028). Para que la Junta de Clasificación lo vea con su propio sistema, tiene que quedar en sus tablas, con sus claves y códigos. La DBA confirmó que en las bases de desarrollo y prueba se pueden agregar registros en estas tablas:

| Acción en el portal | Tabla |
|---|---|
| Inscripción del Formulario C | `T_INSCRIPCIONES_JD` |
| Materias y cargos elegidos | `T_DETALLES_INSC_JD` |
| Antecedente cargado (pendiente, rechazado o dado de baja) | `T_EMP_ITEMS_VARIO_JD`, `T_EMP_ITEMS_CONCEPTO_JD`, `T_EMP_ITEM_EST_PARCIAL_JD` |
| Antecedente validado por el vocal | `T_ITEMS_VARIO_JD`, `T_ITEMS_CONCEPTO_JD`, `T_ITEMS_EST_PARCIAL_JD` |
| Antigüedad | `T_ITEMS_ANTIGUEDAD_JD` |

El relevamiento del diccionario de datos y del código de los disparadores de la base de desarrollo (MT4D) dio:

- Los disparadores solo completan usuario y fecha de alta o modificación y guardan el historial. `T_EMP_ITEMS_VARIO_JD.ID_ORDINAL` lo asigna el disparador con la secuencia `SEQ_ITEM_VARIO_JD` cuando llega vacío; las filas validadas de `T_ITEMS_VARIO_JD` usan la misma secuencia.
- `FOLIO` vale `'0'` en todas las inscripciones; `ID_SEQ_INSCRIPCION_JD` no se usa.
- El docente se identifica con `ID_SOCIEDAD` (`PE`) e `ID_EMPLEADO`, que no se deriva del CUIL: se busca en `VT_EMPLEADOS`.
- La escuela base (`ID_EMPRESA_BASE`) es una de las escuelas elegidas en el 91 % de las inscripciones; el 97 % de los docentes tiene una sola inscripción por convocatoria.
- Los cursos van al ítem 05 y las jornadas al 07, con el subítem genérico `010`; las horas válidas están en `T_ITEMS_HORA`. El concepto es el ítem 17 (1 distinguido, 2 sobresaliente, 3 bueno, 4 regular, 5 malo) en el nivel educativo 2 (medio).

## Decisión

- **PostgreSQL sigue siendo la persistencia del portal.** El espejo envuelve la persistencia (`conEspejoJunta`): cada vez que se guarda el legajo o el Formulario C de un docente, lleva esa foto a las tablas de la Junta en una transacción aparte. Un error en Oracle se registra y no frena al portal.
- **Traducción a la forma de la Junta** (`server/src/store/persistencia/oracle-junta.ts`): CUIL → `ID_EMPLEADO`; estados del legajo → `P` pendiente, `V` validado, `R` rechazado, `E` eliminado por el aspirante; categoría → ítem y subítem; el motivo del rechazo va en `OBSERVACION2`. El Formulario C se inscribe en la convocatoria configurada, con la primera escuela elegida como escuela base y una fila de detalle por escuela y materia o cargo, numeradas en orden.
- **Sincronización idempotente:** las filas del portal llevan la marca `PD24:` en `COMENT`. Repetir la sincronización no duplica filas, y las filas cargadas por la Junta nunca se modifican ni se borran.
- **Lista cerrada** (`oracle-resguardo.ts`): toda sentencia pasa por el resguardo. Las lecturas son libres; las escrituras solo se aceptan sobre las nueve tablas de la lista, una sentencia por vez, sin DDL ni subconsultas en los `INSERT`, y los `UPDATE` y `DELETE` siempre filtran por `ID_EMPLEADO`.
- **Solo desarrollo y prueba:** antes de escribir se verifica el nombre de la base (`JUNTA_BASES_PERMITIDAS`, por defecto `MT4D,MT4T`). La base de producción no está en la lista.
- **Tres modos** con `ESCRITURA_JUNTA`: `off` (por defecto, no escribe), `ensayo` (ejecuta todo y deshace con `ROLLBACK`) y `on` (confirma con `COMMIT`).
- `npm run junta:probar --workspace=server` ejecuta un legajo y un Formulario C de ejemplo en modo ensayo contra el lab y verifica que no quede ninguna fila del portal.

## Consecuencias

**Positivas**

- Lo que carga el aspirante y valida el vocal queda en la forma que usa la Junta; pasar a la base real es cambiar la conexión y el modo.
- El modo ensayo prueba claves foráneas, disparadores y secuencias reales sin dejar datos.
- La lista cerrada y la verificación de la base acotan el daño posible aunque el usuario de la conexión sea dueño del esquema.

**Negativas**

- El modo ensayo no deja filas, pero las secuencias avanzan: quedan números de `ID_ORDINAL` sin usar.
- Por ahora se espejan los cursos (05), las jornadas (07), el concepto (17) y la inscripción. Los títulos no tienen tabla de pendientes en la Junta; los estudios parciales necesitan el código del título y el concepto necesita el código de la escuela, que el formulario del legajo todavía pide como texto.
- La escuela base y el desplegable de motivos de rechazo (`T_EMP_MOTIVOS_JD`) quedan como próximos pasos en la interfaz.

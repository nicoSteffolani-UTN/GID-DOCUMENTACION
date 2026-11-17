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
- **Traducción a la forma de la Junta** (`server/src/store/persistencia/oracle-junta.ts`): CUIL → `ID_EMPLEADO`; estados del legajo → `P` pendiente, `V` validado, `R` rechazado, `E` eliminado por el aspirante; categoría → ítem y subítem; el motivo del rechazo va en `OBSERVACION2`. El Formulario C se inscribe en la convocatoria configurada, con la escuela base que asigna el sistema (ADR-013, addendum 25/10) y una fila de detalle por escuela y materia o cargo, numeradas en orden.
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
- Los títulos no tienen tabla de pendientes en la Junta y no se espejan. (Corregido en el addendum del 05/11: la tabla existe, `T_EMP_TITULOS_EMPLEADO`, y los títulos se espejan.) (Estudios parciales, concepto con código de escuela y motivos de rechazo se sumaron en el addendum del 25/10.)

## Addendum (25/10/2026): convocatoria

`JUNTA_CONVOCATORIA` ya no toma por defecto el año en curso, que en el lab no tiene inscripciones. Si está vacía, el espejo usa la **convocatoria más reciente con más de 10 docentes inscriptos** en `T_INSCRIPCIONES_JD` (el mismo umbral que el mapa, ADR-019). La consulta tarda menos de medio segundo y se hace una sola vez por arranque. En MT4D elige **2021** (1.191 docentes en 434 escuelas); 2017–2019 tienen alrededor de 60.000 cada una. Si se quiere probar con una convocatoria de volumen, se fija `JUNTA_CONVOCATORIA=2019`.

**En producción** la convocatoria va siempre fija en `JUNTA_CONVOCATORIA`, con la convocatoria abierta: la automática solo tiene sentido con datos de prueba, porque inscribir en la última convocatoria con datos sería inscribir en una ya cerrada. Además, el espejo solo escribe en las bases de `JUNTA_BASES_PERMITIDAS` (desarrollo y prueba).

## Addendum (25/10/2026): espejo completo del legajo

El espejo cubre ahora **todo lo que tiene tabla de pendientes en la Junta**. El circuito lo confirmó el DBA: el aspirante carga en `T_EMP_ITEMS_VARIO_JD`, `T_EMP_ITEMS_CONCEPTO_JD` y `T_EMP_ITEM_EST_PARCIAL_JD` (ITEM en singular, verificado en el lab), y lo que valida el vocal pasa a `T_ITEMS_*_JD`, que son las tablas que usa el cálculo del puntaje (un proceso de la base, fuera del alcance del portal).

- **Tipo de antecedente.** Cada categoría que va a `T_EMP_ITEMS_VARIO_JD` ofrece sus subítems vigentes del Dto. 257/19 (`T_SUBITEMS.ACTIVO_ASIG_CGO_DTO257 = 'S'`) y el docente elige uno (`tipoJunta`, p. ej. `05/8D`). Correspondencia: desde el 05/11 los rubros son los de la Junta (addendum del ADR-017): formación pos-inicial → 05 (el docente elige entre los 7 subítems de su formulario; 07, 08, 16 y 42 solo llegan sincronizados); trabajo institucional → 43, 44, 45, 12, 11 (premios) y 46 (capacitación del Art. 19); publicaciones, comisiones, becas, adscripciones y desempeño → 09, 10, 04, 29 y 14; concursos → 06. Lo cargado antes, sin tipo, conserva el genérico 05/010; las demás categorías sin tipo no se espejan.
- **Estudios parciales** (ítem 03, Art. 27): el título se elige del catálogo y el espejo busca su `ID_TITULO` por nombre en `T_TITULOS` (todos los títulos del catálogo del validador están ahí). Los años aprobados son el subítem: 01 a 05. Al validar, `T_ITEMS_EST_PARCIAL_JD` exige `ID_ALCANCE`: se usa **90**, "afín a todos los cargos y asignaturas", el más usado. La Junta admite un solo estudio parcial validado por título; si ya hay uno de la Junta, el portal no lo toca.
- **Concepto:** la escuela se elige con el buscador del padrón y se guarda su código de empresa (`establecimientoId`).
- **Rechazo:** el vocal elige un motivo de `T_EMP_MOTIVOS_JD` (`fixtures/motivos-rechazo.json`, `GET /api/junta/motivos`) y puede agregar un detalle. El espejo escribe `ID_MOTIVO` y el texto en `OBSERVACION2`. Sigue aceptándose texto libre de 10 caracteres o más, por compatibilidad. En Postgres el código va en `antecedente_observacion.motivo_id` (migración `006`).
- **Trazabilidad:** aceptar y rechazar guardan quién y cuándo (`revision`). El espejo lo lleva a `ID_USUARIO_MODIF` = `A` + DNI del vocal y a `FEC_MODIF` en la fila del aspirante, y a `ID_USUARIO_ALTA` en la validada. También se actualiza la fila cuando cambia solo el motivo de un rechazo.
- `npm run junta:probar` suma una beca (04) y un estudio parcial; todo pasa en ensayo contra MT4D y no deja filas.

**Quedan en el portal:** títulos y la antigüedad que declara el aspirante (la Junta no tiene tablas de pendientes para ellos), los PDF, las alertas y los intereses del mapa ("mis escuelas"), que no tienen equivalente en el sistema de la Junta: falta consultar dónde guardarlos. La inscripción en Postgres sigue siendo una por CUIL; el historial por convocatoria queda en `T_INSCRIPCIONES_JD`.

## Addendum (26/10/2026): antigüedad, respuesta del DBA

- `T_ITEMS_ANTIGUEDAD_JD` guarda la antigüedad de servicios **en otras provincias o en el ámbito privado**, que es la que el aspirante carga a mano en el legajo. No tiene tabla de pendientes (`T_EMP_*`): se escribe recién cuando el vocal la acepta.
- Para escribirla hacen falta el subítem (frente a alumnos 011/012 o no frente a alumnos 021/022) y la zona (normal o desfavorable). Esos datos los carga el vocal desde la constancia de servicios (rama `feature/antiguedad-vocal`). El espejo sigue salteando la tabla hasta integrar ese trabajo.
- La antigüedad provincial está en `PEOPLENET.M4CBA_VW_CONSE_PUB`. Falta relevar sus columnas y decidir si reemplaza a `M4CBA_VW_CONC_DIR` en el perfil (ADR-030).
- Siguen abiertas con el DBA: cómo dejar constancia del vocal que validó (los disparadores reemplazan el usuario), cómo obtiene su `ID_EMPLEADO` un aspirante nuevo, quién numera el `FOLIO` y dónde guardar los intereses del mapa.

## Addendum (04/11/2026): espejo de la antigüedad

El espejo ya no saltea `T_ITEMS_ANTIGUEDAD_JD`: lleva la antigüedad que carga el vocal desde la constancia de servicios. Relevado en MT4D:

- La clave es `ID_SOCIEDAD`, `ID_EMPLEADO`, `ID_ITEM` e `ID_SUBITEM`, con clave foránea a `T_SUBITEMS`: los 37 ítems y subítems del catálogo del portal existen ahí. `ANT_ZONA_NORMAL` y `ANT_ZONA_DESFAVORABLE` son `NUMBER(2)` sin decimales, así que **los años son enteros** en el formulario y en la API. `DIRECTOR` está vacía en todas las filas y no se escribe.
- Los disparadores completan usuario y fecha y guardan el historial en `T_HIST_ITEMS_ANTIGUEDAD_JD`.
- Sincronización: una fila por ítem y subítem, con `COMENT = 'PD24:antiguedad'`. Si la fila no existe se da de alta; si es del portal se actualizan los años o se borra cuando el vocal la quita. **Si la Junta ya tiene esa clave, no se toca**, aunque los años difieran: queda registrado en el resultado de la sincronización y el dato de la Junta sigue siendo el de su sistema.
- Solo va la antigüedad que cargó el vocal. La que declara el aspirante sigue quedando en el portal.

## Addendum (05/11/2026): los títulos se espejan en T_EMP_TITULOS_EMPLEADO

- Lo que decía este ADR («los títulos no tienen tabla de pendientes») no era así: la web de la Junta guarda los títulos que carga el aspirante en `T_EMP_TITULOS_EMPLEADO` (184 filas en MT4D), con los mismos estados que los demás pendientes (P, V, R, E) y disparadores de alta y modificación.
- El espejo escribe ahí los títulos de origen `MANUAL`, con la marca `PD24:<id>` en `COMENT`. La tabla se suma a la lista cerrada del resguardo (10 tablas).
  - Clave primaria `ID_EMPLEADO` + `POSICION`: se toma la mayor posición del docente entre `T_EMP_TITULOS_EMPLEADO` y `T_TITULOS_EMPLEADO`, más uno.
  - `ID_TITULO` se busca por nombre en `T_TITULOS`, igual que en estudios parciales. Si no aparece, el nombre va en `OTROS_TITULO`.
  - Universidad, facultad y título tienen clave foránea compuesta a `T_TITULOS_FACULTAD`. Si la institución se eligió del buscador del legajo y la combinación existe, van los códigos. Si no, la institución va como texto en `OTROS_UNIFAC`, que es lo que hace la web de la Junta con «Otros».
  - Analítico Definitivo/Provisorio → `PROVISORIO` N/S; promedio → `PROMEDIO` (NUMBER(4,2)); fin de estudio → `A_FIN_ESTUDIOS`; plan → `OTROS_PLAN`; `TERMINADO = 'S'`.
- Lo validado por la Junta (`T_TITULOS_EMPLEADO`) **no** se escribe: el vocal del portal cambia el estado en la tabla de pendientes y la copia al registro oficial queda para la Junta.
- Probado con `npm run junta:probar` en modo ensayo contra MT4D: alta con institución como texto y alta con códigos del catálogo (pasa la clave foránea), y ninguna fila del portal en la base después del ROLLBACK.
- Consecuencia: pasar `ESCRITURA_JUNTA=on` ahora también escribe títulos. Antes de hacerlo en una base compartida, confirmarlo con el DBA, igual que el resto del espejo.

## Addendum (05/11/2026): corrección de datos

El espejo ya no actualiza solo el estado: si el vocal corrigió un antecedente y el dato difiere, actualiza la fila del portal y su copia
validada (ADR-034). Los antecedentes sin corrección no cambian, y la fila solo se reconoce por la marca PD24. `npm run junta:probar` lo
ensaya con el alta, la corrección y su repetición en una misma transacción, que se deshace.

## Addendum (05/11/2026): valoración, motivo y antigüedad declarada

Corrige lo dicho el 26/10: la antigüedad que declara el aspirante sí tiene pendientes, en `T_EMP_ANTIGUEDADES`.

- **Valoración del título (ADR-033):** el caso que decide el vocal va a `ID_TITULO_HABILITACION` y `VALORACION_TITULO` de las filas de `T_DETALLES_INSC_JD` con la marca del portal. La sugerencia no se escribe: hasta que el vocal decida, las columnas quedan vacías. Las inscripciones de la Junta no se tocan. Como el detalle se rearma al guardar el Formulario C, los casos se leen antes de borrarlo y se vuelven a cargar.
- **Motivo de la corrección (ADR-034):** va en `COMENT`, después de la marca (`PD24:<id> Corregido: <motivo>`), hasta 200 caracteres. La marca que identifica la fila es lo que está antes del primer espacio.
- **Antigüedad declarada:** va a `T_EMP_ANTIGUEDADES`, que se suma a la lista cerrada del resguardo (11 tablas). No tiene `COMENT`: la marca, el cargo escrito a mano y el motivo van en `OBSERVACIONES`. La clave sale de `SEQ_EMP_ANTIGUEDADES`, la provincia se traduce con `PEOPLENET.M4_DIV_GEOGRAFICA` y el cargo con los códigos de `T_GRADOS_JUNTA`. `PERTENECE_JD = 'S'`; `ID_ITEM` e `ID_SUBITEM` quedan vacíos, como en la mayoría de las filas del lab. Lo validado sigue siendo lo que carga el vocal en `T_ITEMS_ANTIGUEDAD_JD`.
- Consecuencia: con `ESCRITURA_JUNTA=on` también se escribe todo esto. `npm run junta:probar` lo ensaya en una transacción que se deshace.

## Addendum (06/11/2026): campos propios de los rubros sin formulario y código de emisor

El video de la cátedra no muestra Trabajo Institucional, Publicaciones… ni Concursos, así que se tomó lo que la Junta llena en `T_EMP_ITEMS_VARIO_JD`. Las muestras son chicas (de 1 a 10 filas por ítem): conviene confirmarlo con el formulario real.

- **Fecha:** obligatoria en los tres rubros; va a `FECHA_TITULO`, presente en el 100 % de esas filas. Las banderas de `T_ITEMS` no sirven de guía: marcan `FECHA_TITULO = N` en varios de estos ítems aunque las filas la tengan.
- **Becas (ítem 04):** «otorgada por» obligatorio, va a `OTORGADO` (`T_ITEMS.OTORGADO = S` y 100 % de las filas).
- **Concursos (ítem 06):** año, número de resolución, decreto de designación y cargo, optativos. El cargo solo va a `ID_AGRUPAMIENTO`, `ID_NIVEL` e `ID_GRADO_JUNTA` si se eligió del buscador: tienen clave foránea a `T_GRADOS_JUNTA`. Escrito a mano queda solo en el portal.
- **Emisor de formación:** va a `ID_EMISOR` (`T_CAP_EMISORES`: 1 Red Provincial, 2 ISEP, 3 Otro). `COMENT_EMISOR` solo con «Otro», donde pasa a ser obligatorio (las 6 filas con ese código lo tienen). `OTORGADO` deja de llevar el emisor: en los pendientes del ítem 05 casi no se usa. Un emisor escrito a mano en cargas viejas va como «Otro» con su nombre.
- Consecuencias: las filas ya escritas no cambian, salvo que el vocal las corrija. Negativa: si el vocal corrige un antecedente anterior a este cambio, tiene que completar la fecha, porque la corrección usa las mismas reglas que la carga (ADR-034).

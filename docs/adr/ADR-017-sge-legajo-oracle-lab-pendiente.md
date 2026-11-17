# ADR-017: SGE legajo provincial — pendiente Oracle lab

**Estado:** Aceptado (addendum 15/10: antecedentes ya validados por la Junta desde el lab)  
**Fecha:** 2026-08-24  
**Relacionado:** US-03, ADR-011, diagrama 19

## Contexto

El portal importa antecedentes provinciales vía `SgeClient` (`MockSgeAdapter` + fixture). Se exploró cablear lectura desde Oracle lab en modo `INTEGRATION_MODE=prod`.

## Discovery MT4D (24/08)

| Objeto | Hallazgo |
|--------|----------|
| `VT_LEGAJOS_JD` / `VT_LEGAJOS_JP` | Clave `ID_EMPLEADO`, sin CUIL directo |
| `VT_EMPLEADOS` | ~169k filas con CUIL; join posible pero sin antecedentes estructurados |
| `VT_CONTANCIAS_SERVICIO` | Sin columna CUIL; no joinable al login |

## Decisión

**No implementar** `OracleSgeAdapter` en lab hasta que JTP indique:

1. Vista oficial de antecedentes provinciales con **CUIL** o join documentado empleado→CUIL.
2. Mapeo a categorías del portal (`AntecedenteProvinciaFixture`).

Persistencia del legajo importado sigue en JSON local (`user-data.store.ts`); lab es solo lectura.

## Consecuencias

- `INTEGRATION_MODE=prod`: SGE sigue mock para antecedentes provinciales.
- Import manual del aspirante no se enriquece desde Oracle lab.

## Pregunta para JTP

¿Qué vista/tabla usar para antecedentes de servicio con CUIL en MT4T/producción?

## Addendum (15/10/2026): antecedentes validados por la Junta desde el lab

El DBA indicó el circuito de la Junta de secundaria: lo que carga el aspirante por la web va a `T_EMP_ITEMS_*_JD` y, cuando el vocal lo valida, pasa a `T_ITEMS_*_JD`. `VT_EMPLEADOS` da el join CUIL → `ID_EMPLEADO` que faltaba (punto 1 de la decisión).

- En `INTEGRATION_MODE=prod`, `OracleSgeAdapter` (`integrations/sge/oracle-sge.adapter.ts`) trae por request los antecedentes **ya validados**: títulos de `T_TITULOS_EMPLEADO` (nombre de `T_TITULOS.N_TITULO`, el mismo que usa el alcance) y el resto de `T_ITEMS_VARIO_JD`. Lo pendiente de la web (`T_EMP_*`) no se trae.
- El docente se busca por **CUIL exacto** (`REPLACE(CUIL, '-', '')`), nunca por DNI: un CUIL inventado no trae datos de otra persona.
- Mapeo de ítems (`T_ITEMS`): 05 cursos → `formacion_postinicial` con horas, emisor y fecha (`FECHA_TITULO`, si falta `FEC_ALTA`) para que el Dto. 257/19 los valore y aplique la caducidad; 07 y 08 → `cursos_capacitaciones`; 06 → `concursos_docentes`; 09 → `publicaciones`; 10 → `trabajo_institucional`; el resto → `otros_antecedentes` (reagrupado el 05/11/2026, ver addendum). Concepto (`T_ITEMS_CONCEPTO_JD`), estudios parciales (`T_ITEMS_EST_PARCIAL_JD`) y antigüedad de la junta (`T_ITEMS_ANTIGUEDAD_JD`) quedan para después.
- Llegan como `SIN_PENDIENTE` con origen `PROVINCIA` (no se podían borrar; desde el addendum del 05/11 sí). En `mock` y en los tests sigue `antecedentes-provincia.mock.json`; el chip «Datos de ejemplo» del legajo solo aparece fuera de `prod`.
- El puerto `SgeClient` pasa a ser asíncrono.

## Addendum (05/11/2026): el docente elige qué importar y puede quitar provinciales

- El botón del legajo pasa a llamarse «Sincronizar antecedentes» y abre una ventana con la vista previa (`GET /api/legajo/sincronizar-provincia/vista-previa`): lo encontrado se filtra por categoría, el docente marca todos, algunos o ninguno, y solo se importan los marcados (`POST /api/legajo/sincronizar-provincia` con `ids`). Puede descartar de la lista lo que no quiere (no se borra nada: vuelve a aparecer en la próxima sincronización).
- **Cambia la regla anterior**: los antecedentes de origen `PROVINCIA` ya importados **se pueden quitar del legajo** (desde esa ventana, con confirmación). Siguen registrados en el Ministerio y se pueden volver a importar. La antigüedad de origen `JUNTA` sigue sin poder borrarse.
- Consecuencias: quitar un provincial baja el puntaje estimado del portal, pero no toca el Oracle de la Junta: el espejo (ADR-029) solo escribe lo de origen `MANUAL`. Negativa: el legajo del portal puede quedar con menos antecedentes que los que la Junta tiene validados, así que el puntaje estimado puede quedar por debajo del oficial.
- Corrección: un CUIL puede tener más de un `ID_EMPLEADO` (289 en el lab) y `T_TITULOS_EMPLEADO.POSICION` solo es única por `ID_EMPLEADO`, así que dos títulos distintos salían con el mismo `JD-TIT-<POSICION>` y la importación se rechazaba («Algunos antecedentes elegidos no figuran…»). Ahora el mismo título en los dos registros se muestra una vez y, si la posición choca, el id pasa a `JD-TIT-<ID_EMPLEADO>-<POSICION>`; los demás conservan el formato anterior para no duplicar lo ya importado.

## Addendum (15/11/2026): los antecedentes ya no se eliminan

- **Cambia el addendum anterior**: el docente ya no puede quitar ningún antecedente del legajo, ni los provinciales importados ni los que cargó a mano. Lo que la Junta valoró queda en el legajo.
- Se retiran el botón «Quitar del legajo» de «Sincronizar antecedentes», el botón «Eliminar» de «Consultar antecedentes» y el endpoint `DELETE /api/legajo/:id` (responde 404). Descartar de la vista previa sigue igual: no borra nada.
- Negativa: un antecedente rechazado ya no se borra para volver a cargarlo; el camino es el reclamo o cargar uno nuevo.

## Addendum (05/11/2026): conceptos, estudios parciales y antigüedad de la Junta

- La sincronización suma tres tablas que habían quedado «para después», con la misma búsqueda por CUIL exacto:
  - `T_ITEMS_CONCEPTO_JD`, solo ítem 17 (conceptos docentes) y año de cuatro cifras → `concepto_docente` con tipo «Docente», calificación (`T_SUBITEMS.N_SUBITEM`), año y nivel (`ID_NIVEL_EDUC` 2 → Medio, 1 → Primario). En el lab el 99,99 % de los conceptos validados no tiene `ID_EMPRESA`: salen sin escuela.
  - `T_ITEMS_EST_PARCIAL_JD` → `estudios_parciales` con el título de `T_TITULOS`, los años aprobados que dice el subítem («3 AÑOS AFINES») y la fecha de la última materia.
  - `T_ITEMS_ANTIGUEDAD_JD`, solo ítem 02 (antigüedad docente) → `antiguedad` con ítem, subítem y años en zona normal y desfavorable. No se traen las filas con la marca del espejo (`PD24:antiguedad`), que son las que escribió el propio portal.
- De `T_ITEMS_VARIO_JD` se dejan afuera los ítems 36 (concurso directivo), 38 (referencias directivas) y 68 (referencias para inspectores): Formularios D e I están fuera de alcance. Directivos (37) y supervisión (67) tampoco se traen de conceptos, ni la antigüedad directiva (32, 33, 62).
- Con el docente de prueba: 121 antecedentes (6 títulos, 75 cursos con horas, 24 cursos y congresos, 15 conceptos, 1 antigüedad), todos con id distinto, en ~2 s.
- Consecuencias: el puntaje estimado ya valora los conceptos (Art. 24) y los estudios parciales importados. La antigüedad histórica de la Junta usa subítems anteriores al Dto. 257/19 (11, 21, 71…) que el catálogo del portal no tiene: queda «sin valorar» con ese motivo, en lugar de figurar como antigüedad directiva. Negativa: si el vocal vuelve a cargar en el portal una antigüedad que la Junta ya tenía, el estimado la puede contar dos veces.

## Addendum 2026-11-05 — rubros agrupados como en la Junta

- Contexto: la web de la Junta muestra 9 rubros, armados por artículo del Dto. 257/19 (`T_ITEMS` 201–207); el portal tenía 10 con otra agrupación («Cursos y capacitaciones» y «Otros antecedentes» no existen en la Junta).
- Decisión: los rubros pasan a ser los de la Junta, con sus nombres: Títulos; Antigüedades Docentes; Formación Pos-Inicial (Art. 8–13: ítems 05, 07, 08, 16 y 42); Trabajo Institucional para la Mejora de los Aprendizajes (Art. 14–19: 43, 44, 45, 12, 11 y 46); Publicaciones, Comisiones, Becas, Adscripciones y Desempeño (Art. 20–23 y 28: 09, 10, 04, 29 y 14); Concepto Profesional Docente; Estudios Parciales; Concursos Docentes (06). El mapeo vive en `modules/legajo/categorias-junta.ts` y lo usan la sincronización y la migración. Un ítem sin rubro propio va a Publicaciones (la Junta lo llama «Antecedentes docentes, profesionales y otros»).
- Traslados no se suma: es el Formulario T, fuera de alcance, y el video de la cátedra no lo muestra.
- El docente no puede cargar congresos (07), dictado de cursos (08), antecedentes meritorios (16) ni idiomas (42): el desplegable de Concepto de la Junta solo ofrece los 7 subítems del 05. Los que ya validó la Junta se siguen trayendo y se ven en Formación Pos-Inicial.
- Migración: al arrancar, el store pasa los antecedentes guardados con los rubros viejos al nuevo (por el ítem del tipo; sin tipo, `cursos_capacitaciones` → formación pos-inicial y `otros_antecedentes` → publicaciones) y los vuelve a guardar. Negativa: hay que correr `npm run catalogos:importar --workspace=server` para que Postgres tome los rubros nuevos.

## Addendum (15/11/2026): catálogo de títulos de `T_TITULOS` y título «Otro»

- Contexto: el buscador de títulos del legajo (títulos y estudios parciales) solo ofrecía los nombres de `VT_MAT_GRADO_X_TITULO` (2.237), es decir, los que habilitan alguna materia. En el lab, `T_TITULOS` tiene 6.822 títulos vigentes (~4.770 sin `ACTIVO_JS = 'N'`), y el 69 % de los títulos ya cargados a docentes (`T_TITULOS_EMPLEADO`) no figura en esa vista: postítulos, posgrados y títulos sin alcance no se podían elegir. Además, la Junta (reunión del 13/11) pidió la opción «Otro» para los títulos nuevos que todavía no tienen código; hoy los da de alta cuando le avisan.
- Decisión 1: el catálogo suma los títulos de `T_TITULOS` vigentes (sin baja, sin unificar y con `ACTIVO_JS` distinto de `N`) que no están en el alcance, con el mismo código (nombre normalizado) y sin habilitar nada (`sumarTitulosSinAlcance`, `oracle-alcance.adapter.ts`). Si la consulta a `T_TITULOS` falla, el catálogo queda como antes (solo los títulos con alcance) y el arranque sigue. El alcance del Formulario C sigue saliendo de `VT_MAT_GRADO_X_TITULO` (ADR-009). En modo `foto`, los nuevos aparecen cuando se vuelve a tomar la foto.
- Decisión 2: «Otro (mi título no está en la lista)» es una opción fija de los dos buscadores. El docente escribe el nombre como figura en el diploma (3 a 100 caracteres, sin `<` ni `>`) y, en títulos, el nivel (título, postítulo o posgrado); los PDF se piden igual. Se guarda en `tituloOtro`, sin `tituloCodigo`, queda `PENDIENTE` y no se compara con otros títulos por código.
- Vocal: no cambia nada en su módulo. Ve el título con el nombre que escribió el docente y lo acepta o rechaza como cualquier otro; como todo título, no se modifica (regla del 14/11). La valoración del título (ADR-033) y el alcance no lo cuentan porque no tiene código.
- Espejo (ADR-029): sin código no se escribe en `T_EMP_TITULOS_EMPLEADO`; queda anotado en lo que informa la sincronización («título fuera del catálogo sin código: no se espeja»). Los estudios parciales ya no se espejaban sin `ID_TITULO` (es parte de la clave de `T_EMP_ITEM_EST_PARCIAL_JD`).
- Alternativa descartada por ahora: espejar el título «Otro» con `ID_TITULO` nulo y el nombre en `OTROS_TITULO`, columna que existe en `T_EMP_TITULOS_EMPLEADO` (VARCHAR2(100); en el lab, 2 de 184 filas no tienen `ID_TITULO`). Sería lo más parecido a la web de la Junta; queda para confirmar con la Junta y la DBA.
- Negativas y pendientes: preguntar a la DBA qué significa exactamente `ACTIVO_JS` y los códigos de `ID_NIVEL_TITULO` (`PG`, `MAG`, `PSU`, `U01`…), que podrían completar el nivel de los títulos del catálogo. El espejo busca `ID_TITULO` por el nombre normalizado (sin tildes): un nombre de `T_TITULOS` con tildes no se encuentra y va como `OTROS_TITULO` (pasaba antes también).

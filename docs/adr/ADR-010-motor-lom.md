# ADR-010 — Motor LOM (valoración de inscripción)

- **Estado:** Aceptado (contrato definido; implementación prod pendiente ministerio)
- **Fecha:** 2026-07-07
- **Relacionado:** US-05, ADR-009 (alcance título), diagrama 19

## Contexto

El portal debe mostrar el **desglose de puntaje LOM** por cada postulación del Formulario C. En desarrollo se usa `puntaje.mock.json` vía `LomValoracionPort` (`integrations/ports.ts`).

## Decisión

Definir un puerto `LomValoracionPort` con contrato único para mock y motor ministerial:

### Entrada implícita

- `cuil` del aspirante autenticado (JWT).
- Inscripciones vigentes en Formulario C (motor puede cruzar con su propio snapshot).

### Salida — `ValoracionFixture`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `ciclo` | string | Ciclo lectivo (ej. `2026`) |
| `tipoOrden` | `PROVISORIO` \| `DEFINITIVO` | Estado de la lista |
| `filas[]` | array | Una fila por escuela × asignatura/cargo inscripto |
| `filas[].escuelaId` | string | ID interno portal |
| `filas[].escuelaNombre` | string | Denominación |
| `filas[].itemCodigo` | string | Código numérico asignatura/cargo |
| `filas[].itemNombre` | string | Denominación ítem |
| `filas[].itemTipo` | `asignatura` \| `cargo` | Tipo |
| `filas[].tituloBase` | number | Puntos por título habilitante |
| `filas[].antiguedad` | number | Puntos antigüedad |
| `filas[].capacitaciones` | number | Puntos formación/capacitaciones |
| `filas[].total` | number | Suma ponderada |

### Respuesta API (`GET /api/puntaje/inscripcion`)

- Si hay valoración: `{ disponible: true, ciclo, tipoOrden, filas }`.
- Si no: `{ disponible: false, mensaje }` — mensaje genérico al aspirante.

### Errores

- Motor no disponible → 503 con reintento sugerido (prod).
- CUIL sin inscripciones → `disponible: false`.

## Consecuencias

- El adapter mock (`MockLomAdapter`) y el futuro adapter Oracle/API comparten tipos en `puntaje.types.ts`.
- Cambios en reglas LOM del ministerio solo afectan el adapter prod.
- La UI `/puntaje` no debe calcular totales; solo muestra lo devuelto por el motor.

## Pendiente producción

- Endpoint o batch ministerial que reciba CUIL + ciclo.
- Acordar si el motor lee Form C del portal o réplica en BD junta.

## Addendum lab MT4D (2026-08-24)

Con `INTEGRATION_MODE=prod`, adapter **`OracleLomAdapter`** lee `VT_PSX_PUNTAJES_X_EMPLEADO`:

- ~481 filas con CUIL; `PJE_DEFINITIVO` en centésimas (11632 → 116,32).
- **Sin desglose** `tituloBase` / `antiguedad` / `capacitaciones` (quedan en 0); solo `total` y `tipoOrden: DEFINITIVO`.
- `escuelaId` = `ID_ESCUELA_BASE` (código interno, no `EE…` del padrón).
- CUIL demo `20123456789` del mock **no** está en lab; probar con CUIL real del smoke.
- Gap vs contrato completo ADR-010: pendiente motor ministerial desglosado.

## Addendum (2026-10-07): estimación según el Dto. 257/19

Desde el PR #41 el portal muestra, además de la valoración del motor, un **puntaje estimado** cuando no hay valoración oficial o
para explicar el cálculo. Desde ADR-027 esa estimación aplica el **Dto. 257/19** completo (título por condición y nivel,
acumulación, antigüedad, formación, trabajo institucional y otros antecedentes, con sus topes) y devuelve el desglose por
artículo (`desglose` y `partidas` en `GET /api/puntaje/inscripcion`). La valoración del motor LOM sigue siendo la oficial: el
estimado se rotula como tal y como orden provisorio.

## Addendum (2026-11-14): la LOM de secundaria sale de `T_PUNTAJES_JD`

`VT_PSX_PUNTAJES_X_EMPLEADO` (addendum del 24/08) se arma con `T_INSCRIPCIONES_JP` / `T_DETALLES_INSC_JP`: es la Junta de
**primaria**, solo tiene la convocatoria 2024 con 136 docentes y guarda el puntaje en centésimas. Para secundaria era la fuente
equivocada y se deja de usar (`ORACLE_LOM_PUNTAJES_OBJECT` ya no existe).

- **Fuente**: `EDUCAR.T_PUNTAJES_JD`, una fila por docente, convocatoria, escuela (`ID_EMPRESA`), materia u horas
  (`GRADO_MATERIA` = `T_MATERIAS_JUNTA.ID_MATERIA`) o cargo (`T_GRADOS_JUNTA.ID_GRADO_JUNTA`) y tipo de puntaje. `PUNTAJE` ya está
  en puntos: no se divide. En 2019 hay 60.195 docentes con `ID` y el total coincide con `T_DETALLES_INSC_JD.PUNTAJE`.
- **Tipo de orden**: `ID` (definitivo) si la convocatoria lo tiene para el docente; si no, `IP` (provisorio), y así se rotula. PIT,
  PROA, traslados y pruebas no se leen.
- **No clasifica**: `ID_REFERENCIA` distinto de 0 (en el lab casi todas las filas que clasifican lo tienen NULL). Sin puntaje y con
  motivo: 2 «Título no reglamentario», 3 «Asignatura no reglamentaria», el resto de `VT_SUBITEMS` con `ID_ITEM = '28'`.
- **Convocatoria**: siempre la más reciente con datos, con la regla del mapa (ADR-019): más de `LOM_SECUNDARIA_MIN_DOCENTES`
  docentes. Se elige al arrancar (~1,5 s) y se vuelve a elegir una vez por día, así que una convocatoria nueva se toma sola. No
  hay año fijo ni variable de entorno. En pantalla no se muestra el año: solo si el orden es provisorio o definitivo.
- **Consulta por docente**: por CUIL exacto vía `VT_EMPLEADOS` (como `oracle-sge.adapter.ts`), en cada request (~30 ms, la PK
  arranca por `ID_SOCIEDAD + ID_EMPLEADO`), como la antigüedad (ADR-030). El middleware `precargarLom` la hace antes de puntaje,
  perfil, mapa, simulador, capacitaciones y asistente, y el adaptador guarda el resultado 10 minutos; esos módulos lo leen de ahí
  sin cambiar sus firmas.
- **Perfil y mapa**: `puntajeReferencia` es el mayor de las filas que clasifican, con la convocatoria y si es provisorio o
  definitivo. Sin filas o sin ninguna que clasifique: `null` → «No valorado». **Nunca más el estimado.**
- **Foto** (`INTEGRATION_MODE=foto`): el puntaje por docente es dato personal y no entra en la copia: «No valorado».
- Sigue sin desglose (título, antigüedad y capacitaciones en 0): el desglose está en `T_PUNTAJE_ITEMS_JD` (pendiente).
- El reporte del vocal (US-24) usa la valoración que esté en la caché: si el aspirante no entró en los últimos 10 minutos, muestra el
  estimado o «sin puntaje», como antes para quien no figuraba en el lab.

## Addendum (2026-11-15): «Mis puntajes» solo con la LOM y su desglose

Pedido de la Junta en la reunión del 13/11: nada de puntaje estimado en la pantalla del docente. Se ve el puntaje de la LOM
vigente si está inscripto y, si no, «No valorado». No es uno solo: hay uno por escuela y por materia (horas cátedra) o cargo.

- **Pantalla**: «Mi puntaje» pasa a **«Mis puntajes»**. Agrupada por escuela y, en cada una, horas cátedra y cargos por separado.
  Por fila: materia o cargo, puntaje oficial, provisorio o definitivo y convocatoria (ahora sí se muestra el año: la Junta lo pidió).
  Se sacan el estimado con el Dto. 257/19, su desglose por artículo, «Es una estimación», los antecedentes que suman o esperan
  revisión y lo que sumarían los pendientes. Los cargos directivos son de otro formulario y quedan fuera.
- **Desglose**: `EDUCAR.T_PUNTAJE_ITEMS_JD` (misma clave que `T_PUNTAJES_JD` + `ID_ITEM`, con `PUNTAJE`), con el nombre de
  `T_ITEMS.N_ITEM`. Segunda consulta por docente (solo `SELECT`, en paralelo con la de puntajes); se une a cada fila por
  `ID_EMPRESA`, `GRADO_MATERIA`, `ID_TIPO_PUNTAJE` y `FEC_PUNTAJE`. Ítems de mayor a menor, sin los que valen 0. `ID_ARTICULO` no
  se lee y el nombre visible no nombra el decreto ni artículos.
- **Si los ítems no suman el total** (~2 % de una muestra de 2019): el total que se muestra es el oficial y la pantalla avisa que el
  detalle no lo suma. La diferencia no se inventa ni se reparte.
- **No clasifica**: «No valorado» con el motivo (addendum anterior). **Sin LOM**: «No valorado: todavía no figurás en la Lista de
  Orden de Mérito vigente», con acceso al Formulario C.
- **Comparación con los inscriptos** (US-16, ADR-037): queda como propuesta académica para la tesis, sin estimado. La marca del
  docente es su puntaje LOM; donde es «No valorado» (o sin LOM, en lo inscripto en el Formulario C) se ve la distribución sin marca.
- **API**: `GET /api/puntaje/inscripcion` devuelve solo `origen: LOM`, `filas` con `items` y `noClasifican`; ya no devuelve
  `desglose`, `oficial`, `revision` ni `pendientesRevision`.
- El estimado sigue fuera de esta pantalla (`PuntajeService.estimarInscripcion`): reporte del vocal (US-24), asistente y simulador.

## Addendum (2026-11-15, tarde): como el sistema original, sin año en pantalla

Revisión de «Mis puntajes» contra las pantallas del sistema original (Formulario C impreso y «Obtener LOM»).

- **Sin año en pantalla.** Se saca «Convocatoria {año}» de las filas y del aviso, y «datos históricos» de la comparación. La
  convocatoria se sigue eligiendo sola (la más reciente con datos, revisada cada día, como el mapa): si el lab trae una nueva, se
  usa sin cambiar código. `ciclo` sigue en la respuesta, pero el front no lo muestra.
- **Alcance de cada materia**, como la columna «Alcance» del original: `VALORACION_TITULO` (o, si está vacía,
  `ID_TITULO_HABILITACION`) de `T_DETALLES_INSC_JD` para la misma inscripción. Son los códigos de la valoración del título
  (ADR-033): 01 docente, 02 y 03 habilitante, 06 y 07 supletorio; 05 (no reglamentario) y los vacíos no tienen alcance. En 2019
  la mitad de las filas no lo trae: la pantalla muestra solo el orden (provisorio o definitivo).
- **Escuela base** de la inscripción: `T_DETALLES_INSC_JD.ID_EMPRESA_BASE`, con el nombre de la tabla de escuelas.
- **Referencia**: el texto ahora usa la misma equivalencia que la vista de la Junta `VT_LOMUNICACONV_JD` entre `ID_REFERENCIA`
  y el subítem de `VT_SUBITEMS` (ítem 28): la 4 es el subítem 2 («No posee legajo en esta junta»), la 5 el 3, la 6 el 4, la 9 el
  7, la 10 el 5, la 11 el 6 y la 12 el 8. Antes se buscaba el subítem con el mismo número y la 4 decía «Título no comprendido…».
  La referencia de cargos jerárquicos (ítem 59 de la inscripción) manda sobre las demás. El texto se muestra sin «(REF n)» y en
  minúsculas.
- Las tres columnas salen con subconsultas por la clave de la inscripción (índice `IDX_DINS_EMMAGRACON`); la consulta sigue en
  ~250 ms.
- La LOM recortada de cada escuela («Obtener LOM» del original) está en el addendum del 15/11 de ADR-037.

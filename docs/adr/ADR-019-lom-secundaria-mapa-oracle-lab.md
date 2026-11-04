# ADR-019: Promedio LOM secundaria en el semáforo del mapa (Oracle lab)

**Estado:** Aceptado  
**Fecha:** 2026-09-12  
**Actualizado:** 2026-09-30 (puntaje solo desde `T_DETALLES_INSC_JD`, un puntaje por docente; ver la última sección)  
**Historial:** 2026-09-22 (promedio de los 10 mejores puntajes y preferencia por la convocatoria vigente; PR #39), 2026-09-28 (convocatoria más reciente por escuela y top 5)

## Contexto

El mapa (`/api/mapa/escuelas`, semáforo LOM) se alimentaba de `VT_PSX_PADRON` +
`T_PSX_CENTRO_BASE` (ADR-016). Ese padrón del lab **no contiene secundaria**: en el
relevamiento es 100 % primaria/inicial (convocatoria 2024), por lo que `promedioLom`
quedaba `NULL` en ~todas las escuelas y el semáforo siempre mostraba el estado "sin datos".

Relevamiento del catálogo del lab: el mérito de secundaria **sí existe**, pero en otra
familia de vistas: `VT_LOMUNICAC_JD`, `VT_LOMUNICA_JD`, `VT_LOMJERARQUICO_JD`,
`VT_LOMDIRECCONV_JD`, `VT_LOMTITU1CONV_JD`, `VT_LOMTITULAR1CONV_JD`, `VT_LOMTITU2CONV_JD`,
`VT_LOMUNICACONV_JD` ("ORDEN DE MERITO DE LA ESCUELA / PROVINCIAL", columna `ORDEN`),
construidas sobre `t_detalles_insc_jd` + `t_publicaciones` + `t_publicaciones_aspirante`,
filtradas a `tipo_proceso='VA'`, `id_nivel_educ='2'` (secundario), `formulario='C'`.

## Decisión

> **Reemplazada en parte por la actualización del 2026-09-30:** el cruce con `T_PUBLICACIONES` y
> `T_PUBLICACIONES_ASPIRANTE` descrito abajo ya no se usa. Se conserva como historial.

Con `INTEGRATION_MODE=prod` el `OracleMapaAdapter` calcula el promedio LOM por escuela
replicando **solo la rama "ORDEN DE MÉRITO DE LA ESCUELA" (ORDEN 3)** de esas vistas, sobre
las **tablas base**:

- LOM secundaria: `EDUCAR.T_DETALLES_INSC_JD` (`ID_REFERENCIA = 0`, `ID_SOCIEDAD='PE'`,
  `FEC_BAJA_LOGICA IS NULL`, convocatoria del subquery de `EDUCAR.T_PROCESOS_CONV`
  con `tipo_proceso='VA'`, `id_nivel_educ='2'`, `formulario='C'`)
- Publicación → empresa: `EDUCAR.T_PUBLICACIONES` (`ID_SIT_REVISTA IN ('03','05')`,
  `GRADO_MATERIA = NVL(i.ID_MATERIA, i.ID_GRADO_JUNTA)`)
- Aspirantes inscriptos: `EDUCAR.T_PUBLICACIONES_ASPIRANTE`
- Escuelas (capa base): `GEDUC.VT_EMP_EMPRESAS_DOM` (secundarias estatales georreferenciadas)

Métrica por escuela (vigente desde 2026-09-22):

- Se ordenan los puntajes de cada escuela de mayor a menor (`ROW_NUMBER() OVER (PARTITION BY p.ID_EMPRESA ORDER BY i.PUNTAJE DESC)`) y se toman los **10 mejores** (`LOM_SECUNDARIA_TOP_N = 10`).
- `promedioLom` = `ROUND(AVG(PUNTAJE), 2)` sobre esos 10; `cantidadDocentes` = `COUNT(DISTINCT ID_EMPLEADO)` sobre las mismas filas (por lo tanto, como máximo 10).
- `PUNTAJE` ya está en escala 0-100 (no se divide por 100 como en ADR-010).

Motivo del top 10: no castigar a las escuelas con mucha escala docente (muchos inscriptos) en el promedio.

Selección de la convocatoria (`LOM_SECUNDARIA_PROCESO`): entre los procesos `tipo_proceso='VA'`, `id_nivel_educ='2'`, `formulario='C'` de `T_PROCESOS_CONV`, se prefiere la convocatoria `2025` si tiene al menos 100 inscripciones activas; si no, la de mayor volumen.

La versión inicial (2026-09-12) promediaba todos los puntajes de la escuela y tomaba la única convocatoria disponible.

## Por qué tablas base y no las vistas `VT_LOM*_JD`

`VT_LOM*_JD` son UNION triples con `NOT EXISTS` (rama provincial) sobre
`t_detalles_insc_jd`: **materializarlas por completo supera los 100 s** en lab (NJS-123).
El join equivalente sobre las tablas base corre en **~3 s** y cubre ~680 de 819 secundarias
(medido 2026-09-12: 681 filas → 678 con promedio tras el merge del adapter).

## Advertencia: datos históricos

Al 2026-09-12, la convocatoria que resolvía el subquery de `T_PROCESOS_CONV` era la única VA nivel 2
Formulario C del lab: **"VACANTES APEX" 2018**. Los promedios del semáforo son por lo tanto
**históricos** (mérito de esa convocatoria, no de la vigente) mientras el lab no exponga una convocatoria
2025 con al menos 100 inscripciones. Se aclara en:

- comentarios del adapter (`oracle-mapa.adapter.ts`),
- log de arranque de `initIntegrations()` (`container.ts`),
- salida del smoke (`smoke-oracle-mapa.ts`),
- este ADR, y
- `src/.env.example`.

## tuPuntaje del aspirante (contra quién se compara el promedio)

- Con `INTEGRATION_MODE=prod`, la valoración del docente viene de
  `VT_PSX_PUNTAJES_X_EMPLEADO` (ADR-010) y se expone como `tuPuntaje` (= max de sus filas).
- Como el CUIL demo (`20123456789`) no figura en esas 136 filas del lab, `container.ts`
  resuelve la LOM del mapa con el compuesto **`LomConRespaldoDemo`**: usa el adapter Oracle
  cuando el CUIL tiene valoración y, si no, cae al fixture `puntaje.mock.json`
  (sin tocar datos reales). Por eso en la demo `tuPuntaje=78.5`.
- El semáforo es **relativo**: verde si `promedioLom ≤ tuPuntaje`, amarillo hasta +10,
  rojo por encima, azul sin promedio. Como los promedios históricos del lab promedian ~38
  (máx ~66,8), con la persona demo (78,5) el mapa queda casi todo verde: es el resultado
  correcto para ese caso, no un error.

## Consecuencias

- Con VPN al lab, el semáforo del mapa ya compara contra LOM secundaria real
  (~680 escuelas con promedio; el resto queda en estado "sin datos").
- El fixture `mapa-lom.mock.json` se mantiene como fuente demo/tests (la única forma
  de ejercitar el semáforo sin VPN, con datos sintéticos).
- El promedio compara con `tuPuntaje` del aspirante (mismo criterio de semáforo verde/amarillo/rojo).
- Si el lab carga la convocatoria VA nivel 2 `2025` con volumen suficiente, el subquery de
  `T_PROCESOS_CONV` la toma automáticamente (sin cambios de código). Otra convocatoria nueva solo
  se toma si supera en volumen a las existentes; preferir otro año requiere cambiar `LOM_SECUNDARIA_PROCESO`.
- Las mediciones de cobertura y tiempos de la sección anterior corresponden a la versión del
  2026-09-12 (promedio completo); no se volvieron a medir con el top 10.

## Actualización (2026-09-28): convocatoria más reciente por escuela y top 5

El cálculo pasó de "convocatoria fija (VA 2018) + top 10" a una selección por escuela:

- Por cada escuela se elige la **convocatoria más reciente** en la que tenga **más de 10
  docentes** (distintos `ID_EMPLEADO`). Si la escuela ya quedó cubierta por una convocatoria
  posterior, **no** se reutiliza en años anteriores.
- `promedioLom` = promedio de los **5 mejores puntajes** (`LOM_SECUNDARIA_TOP_N = 5`) de esa
  escuela en esa convocatoria; `cantidadDocentes` = distintos de esos 5 (≤ 5).
- Se eliminó el subquery `LOM_SECUNDARIA_PROCESO` (ya no hay convocatoria fija).
- El semáforo (verde/amarillo/rojo/azul) **no cambia**.

En el lab (MT4D) hay escuelas con >10 docentes en 2021 (7), 2020 (7), 2019 (512), 2018 (491)
y 2017 (434); el resto queda sin promedio. La consulta tarda ~95 s y devuelve ~530 escuelas
con promedio (444 tras el filtro de coordenadas).

## Actualización (2026-09-30): solo `T_DETALLES_INSC_JD`, un puntaje por docente

**Problema.** El experto de la base confirmó que en `T_DETALLES_INSC_JD` un docente tiene una fila por
cada escuela (`ID_EMPRESA`) y por cada materia o cargo en que se inscribió; la escuela base
(`ID_EMPRESA_BASE`) es una sola. Medido en el lab (`ID_SOCIEDAD='PE'`, `ID_REFERENCIA=0`, vigentes):
el 87 % de los pares (escuela, docente) tienen varias filas, hasta 6. Con la consulta anterior, el
"top 5" era un top 5 de **filas**, no de docentes: un mismo docente podía aparecer 2 o 3 veces y
sesgar el promedio. Además, los `INNER JOIN` con `T_PUBLICACIONES` (por escuela y materia) y
`T_PUBLICACIONES_ASPIRANTE` multiplicaban todavía más las filas, hacían que la consulta tardara
**~100 s** y dejaban fuera a las escuelas sin postulaciones. Esas dos tablas sirven para publicar
cargos; el puntaje definitivo está en `T_DETALLES_INSC_JD`.

**Decisión.** El promedio LOM del mapa sale **únicamente** de `T_DETALLES_INSC_JD`:

1. Un puntaje por (escuela, convocatoria, docente): `MAX(PUNTAJE)` agrupando por `ID_EMPRESA`,
   `ID_CONVOCATORIA`, `ID_EMPLEADO`. Si el docente se inscribió a varias materias, cuenta el mejor.
2. Por escuela se elige la convocatoria **más reciente con más de 10 docentes** distintos
   (`LOM_SECUNDARIA_MIN_DOCENTES = 10`). `ID_CONVOCATORIA` es el año.
3. `promedioLom` = promedio de los **5 mejores docentes** (`LOM_SECUNDARIA_TOP_N = 5`) de esa
   convocatoria; `cantidadDocentes` ≤ 5. Ni escuelas ni docentes se repiten.
4. Se mantienen los filtros `ID_SOCIEDAD='PE'`, `FEC_BAJA_LOGICA IS NULL`, `ID_REFERENCIA = 0` y la
   exclusión de cargos directivos (`D`, `J`, `R`): el mapa solo considera cargos y horas (Formulario C).
5. Las escuelas siguen saliendo de `GEDUC.VT_EMP_EMPRESAS_DOM` (secundarias estatales georreferenciadas).

Se eliminaron `ORACLE_MAPA_LOM_PUBLICACIONES_OBJECT` y `ORACLE_MAPA_LOM_ASPIRANTES_OBJECT`, y los
campos `lomPublicaciones` y `lomAspirantes` de `/api/health` (`sourceObjects`).

**Resultado medido (MT4D, 2026-09-30).**

| | Antes (3 tablas) | Ahora (solo detalles, dedup) |
|---|---|---|
| Tiempo de la consulta | ~100 s | ~2,5 s |
| Escuelas con promedio (de 819) | 444 | 719 |

Los promedios cambian en la mayoría de las escuelas (los anteriores estaban sesgados a la baja).
En 100 escuelas el valor es idéntico y ninguna escuela que tenía promedio lo perdió.

**Datos disponibles.** `ID_CONVOCATORIA` en el lab: 2017 (~517 mil filas), 2018 (~546 mil), 2019 (~636 mil),
2020 y 2021 (~12,6 mil filas, 409 escuelas) y 2024 (11 filas); no hay 2022, 2023 ni 2025. Por eso casi
todas las escuelas resuelven con 2017-2019 y los promedios siguen siendo **históricos**.

**Escala.** El puntaje puede superar 100 (hay promedios de 105 a 115); es correcto y no se normaliza.
El semáforo (verde/amarillo/rojo/azul) no cambia.

## Addendum 2026-10-07 — sedes, anexos y extensiones áulicas

`GEDUC.VT_EMP_EMPRESAS_DOM` trae **una fila por sede**: el mismo `ID_EMPRESA` se repite para la escuela
madre y sus extensiones áulicas (p. ej. CENMA N° 215 con 3 sedes), y una misma sede puede venir repetida por
cada tipo de educación (ProA San Francisco: COMUN y TECNICA). El `SELECT` (solo lectura, sin cambios en el
lab) suma `ID_SEQ_EMPRESA`, `N_TIPO_EMPRESA`, `CUE_ANEXO` y `DOMICILIO`, y el adapter:

- junta filas con el mismo `ID_SEQ_EMPRESA` (un punto por sede, tipos de educación sumados);
- vincula cada anexo o extensión con su **escuela madre** por CUE (`CUE_ANEXO = '00'`) y el popup lo aclara;
- si el lab le asignó a un anexo el `ID_EMPRESA` de una escuela con **otro CUE**, el promedio LOM de ese ID
  no es suyo: queda **sin LOM** (azul) y **sin registro de interés** (`idDeOtraEscuela`). Hoy son 3:
  anexo Matorrales del CENMA 186 (con el ID del CENMA Oncativo), ext. áulica Arroyo Algodón del CENMA Manuel
  Anselmo Ocampo (ID del CENMA Pozo del Molle) y anexo CENCAR del CENMA 96 (ID del CENMA Villa Nueva).

Ninguna escuela sale del mapa. El interés sigue siendo por `escuelaId` (la escuela madre y todas sus sedes).

### Ubicación: correcciones, departamento por coordenadas y barrios

- **Correcciones puntuales** (`CORRECCIONES_UBICACION`): 10 sedes del interior que el lab trae con departamento o
  localidad de otra (p. ej. los C.E.N.M.A. S.E.P. de Cruz del Eje, Deán Funes y Huinca Renancó como CAPITAL / CORDOBA).
- **Sedes sin departamento** (~98, casi todas sedes y anexos de CENMA y CEIJA con `PRIVADO` nulo): el departamento
  se calcula con los límites oficiales de IDECOR (`fixtures/departamentos-cordoba.geojson`) y la localidad se toma
  de la escuela con dato más cercana del mismo departamento a menos de 3 km; si no hay, queda "Sin localidad"
  (18 escuelas). Validación contra el lab: en las 1.075 escuelas con departamento, el cálculo coincide en 1.062
  (98,8 %); las 13 restantes están junto a un límite o tienen el dato mal en el lab y no se tocan.
- **Barrios**: se descartan los valores que no son nombres (letras sueltas `I` / `N` / `S`, `SD`, `SIN INFORMAR`,
  `-`, números). La misma regla (`esBarrioValido`) se usa en el mapa, el asistente y las alertas de vacantes.
- **Filtros**: departamento es una lista; localidad y barrio son desplegables con búsqueda y en cascada (las
  localidades del departamento elegido, los barrios de esa localidad). El **semáforo** se filtra por color (uno o
  varios: verde, amarillo, rojo, sin datos) en el navegador, porque depende del puntaje de cada aspirante.
- **IDECOR** se usa únicamente para el archivo de límites departamentales; el modo `idecor` del mapa se eliminó
  (ADR-008, addendum 2026-10-07).

## Referencias

- ADR-016 (mapa vacantes Oracle lab) · ADR-010 (motor LOM)
- `oracle-mapa.adapter.ts` · `smoke-oracle-mapa.ts`
- Lab MT4D EDUCAR — vistas `VT_LOM*_JD`, tablas `T_DETALLES_INSC_JD` / `T_PUBLICACIONES` / `T_PUBLICACIONES_ASPIRANTE`
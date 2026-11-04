# ADR-009: Integración alcance título (validador US-07)

**Estado:** Aceptado (MVP académico con mock; dump SQL; Oracle lab MT4)  
**Fecha:** 2026-05-31  
**Actualizado:** 2026-09-28  

## Contexto

Instancia 2 US-07 exige validar si el título del aspirante habilita la postulación a asignatura/cargo según el mapa de competencias (alcance título). En el sistema actual existe una vista/tabla de alcance título en Oracle.

## Decisión

1. **Desarrollo local / CI:** fixture JSON `src/fixtures/alcance-titulo.mock.json` (`INTEGRATION_MODE=mock`, default en tests).
2. **Dump ministerial (sin Oracle live):** `INTEGRATION_MODE=sql` + `ALCANCE_SQL_PATH` al archivo `TITULOS_ALCANCES` (fuera del repo). Adapter `SqlFileAlcanceAdapter`.
3. **Lab / prod Oracle:** `INTEGRATION_MODE=prod` + VPN + TNS (canónico) o Easy Connect.
   - Canónico: `TNS_ADMIN` (carpeta con `tnsnames.ora`), `DB_USER`, `DB_PASSWORD`, `DB_ALIAS` (`MT4D` / `MT4T`).
   - Alternativa: `ORACLE_HOST` + `ORACLE_PORT` + `ORACLE_SERVICE` + `ORACLE_USER` / `ORACLE_PASSWORD` (p. ej. `10.250.11.53:1521/mt4d`).
   - Adapter `OracleAlcanceAdapter` lee al arrancar (`initIntegrations`) la vista lab `VT_MAT_GRADO_X_TITULO` (configurable con `ORACLE_ALCANCE_OBJECT`). El dump `TITULOS_ALCANCES` no existe como tabla en MT4D EDUCAR.
4. **Comportamiento MVP:** ~~bloquear postulación si el título no habilita el ítem~~. Reemplazado por el addendum del 2026-09-28: se advierte y el aspirante puede inscribirse igual.
5. Abstracción en servicio `ValidadorService` para cambiar fuente sin tocar Form C. Matching de título normalizado (acentos, punto final).
6. En dump/Oracle, `ALCANCE` nulo se trata como habilitante. `CODIGO` es asignatura o cargo (`320` Matemática, `X001` Preceptor). El título se cruza por el campo `TITULO` (en la vista lab: `N_TITULO` → `MATERIA_GRADO`).

## Consecuencias

- El fixture de demo replica códigos ministeriales; el dump completo y `tnsnames.ora` quedan fuera del repositorio.
- CI no habla con Oracle (tests con mock / `fromRows`).
- Simultaneidad/caducidad de antecedentes quedan fuera (fase 2 validador — Inst 2).

## Referencias

- Instancia 2 Sprint 0 — US-07  
- Acta junta clasificación 08/05  
- Lab MT4D / MT4T (Gobierno de Córdoba)  

## Addendum lab MT4D — alcance habilitante/supletorio (2026-08-24)

Discovery nocturno: **no es viable** distinguir HABILITANTE vs SUPLETORIO en MT4D EDUCAR:

| Objeto inspeccionado | Resultado |
|---------------------|-----------|
| `T_TITULOS_ALCANCES` | No existe en lab |
| `VT_ALCANCES_JD` | Catálogo de áreas (`ID_ALCANCE`), no cruce título×materia |
| `T_TIPOS_ALCANCE` | Tipos genéricos, sin join útil al validador |

**Decisión:** mantener `OracleAlcanceAdapter` con todo `HABILITANTE` por defecto (comportamiento actual). Pendiente JTP: vista equivalente a `TITULOS_ALCANCES` en MT4T/producción o confirmación de regla ministerial alternativa.

## Addendum — inscripción sin título habilitante (2026-09-28)

**Contexto:** el bloqueo del punto 4 impedía inscribirse en materias o cargos que el título no habilita, aunque la junta
igual evalúa esas inscripciones (sin título habilitante). Instancia 2 ya contemplaba la advertencia como alternativa.

**Decisión:** pasar de bloqueo a advertencia.

1. El Formulario C consulta el validador antes de confirmar. Si el título habilita, muestra la confirmación habitual; si no,
   un diálogo que explica que la inscripción queda sin título habilitante (no suma título base) y ofrece **Inscribirme igual**.
2. `PUT /api/formulario-c` ya no rechaza ítems no habilitados. Sigue validando ventana de inscripción, topes (3 escuelas,
   5 ítems), padrón y catálogo de cada escuela.
3. `GET` y `PUT /api/formulario-c` devuelven cada ítem con `habilitado` y, si corresponde, `tipoCompetencia`. Se calculan al
   leer con los títulos actuales del legajo y no se persisten: si el docente carga el título que faltaba, la marca cambia sola.
4. El puntaje estimado (US-05) ya asignaba 0 de título base a los ítems no habilitados; no cambia.

**Consecuencias:** la UI marca esos ítems como «sin título habilitante» (chip y constancia impresa). El endpoint
`POST /api/validador/habilitacion` no cambia.

## Addendum — tres competencias y alcance híbrido (2026-10-02)

**Problema.** En el dump `TITULOS_ALCANCES` (PDF ministerial convertido a SQL) la columna `ALCANCE` trae tres
valores: `DOCENTE`, `HABILITANTE…` y `SUPLETORIO…` (p. ej. Profesor de Matemática: 320 Matemática `DOCENTE`,
193 Física `SUPLETORIO DE NIVEL SUPERIOR`). El sistema tenía dos fallas encadenadas:

1. `mapAlcanceTipo` solo reconocía `HABILITANTE` y `SUPLETORIO`; `DOCENTE` caía en el default `HABILITANTE`.
2. El validador renombraba toda fila no supletoria como `DOCENTE`, así que un título habilitante sumaba el
   título base de docente (25 en vez de 18 en el baremo provisorio de entonces; desde ADR-027, 60 en vez de 40 según el Dto. 257/19).

Además, en `INTEGRATION_MODE=prod` el alcance salía de `VT_MAT_GRADO_X_TITULO`, que no trae competencia (ver
addendum 2026-08-24): todo quedaba como habilitante y nunca aparecía un supletorio.

**Decisión.**

1. `AlcanceFila.alcance` admite `DOCENTE | HABILITANTE | SUPLETORIO` y el validador devuelve el valor tal cual.
   Con varios títulos vale la mejor competencia: docente > habilitante > supletorio. Sin dato (`NULL`, lab) se
   asume habilitante.
2. **Alcance híbrido:** con `INTEGRATION_MODE=prod` y `ALCANCE_SQL_PATH` definido, el alcance se lee del dump
   (`SqlFileAlcanceAdapter`, una vez al arrancar, en memoria) y padrón, catálogo, mapa, LOM y alertas siguen en el
   lab (MT4D / MT4T). No se consulta el alcance de Oracle. Sin `ALCANCE_SQL_PATH`, comportamiento anterior.
3. El catálogo del Formulario C (`GET /api/formulario-c/catalogo`) devuelve `tipoCompetencia` por ítem según los
   títulos del legajo, y la UI lo muestra en la tarjeta: título docente / habilitante / supletorio / sin título
   habilitante (cuando la materia no figura en el alcance del título).

**Verificación con el dump real** (`01_alcance_titulo.sql`, 29 MB): 74 765 `INSERT` → 74 765 filas parseadas,
2 603 títulos (19 542 docente, 32 863 habilitante, 22 360 supletorio). `/api/health` informa
`alcance.filasCargadas` y `sourceObject: "ALCANCE_SQL_PATH"`; si las filas no coinciden con los `INSERT` del
archivo, hay sentencias partidas en varias líneas (el parser lee una por línea).

**Consecuencias.** El puntaje estimado distingue las tres competencias. El dump no se versiona: cada integrante
define su ruta local en `src/.env`. Sin el dump, en `prod` toda materia habilitada figura como «título habilitante».

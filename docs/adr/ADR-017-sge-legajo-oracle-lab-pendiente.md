# ADR-017: SGE legajo provincial — pendiente Oracle lab

**Estado:** Pendiente JTP (bloqueado en MT4D)  
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
- Mapeo de ítems (`T_ITEMS`): 05 cursos → `formacion_postinicial` con horas, emisor y fecha (`FECHA_TITULO`, si falta `FEC_ALTA`) para que el Dto. 257/19 los valore y aplique la caducidad; 07 y 08 → `cursos_capacitaciones`; 06 → `concursos_docentes`; 09 → `publicaciones`; 10 → `trabajo_institucional`; el resto → `otros_antecedentes`. Concepto (`T_ITEMS_CONCEPTO_JD`), estudios parciales (`T_ITEMS_EST_PARCIAL_JD`) y antigüedad de la junta (`T_ITEMS_ANTIGUEDAD_JD`) quedan para después.
- Llegan como `SIN_PENDIENTE` con origen `PROVINCIA` (no se pueden borrar). En `mock` y en los tests sigue `antecedentes-provincia.mock.json`; el chip «Datos de ejemplo» del legajo solo aparece fuera de `prod`.
- El puerto `SgeClient` pasa a ser asíncrono.

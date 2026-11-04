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

# ADR-015: Padrón escuelas y catálogo Form C desde Oracle lab

**Estado:** Aceptado (MVP lab)  
**Actualizado:** 2026-08-24 (enriquecimiento CUE desde `VM_PSX_EMPRESAS`)

## Contexto

US-04 (Formulario C) necesita buscar establecimientos y listar materias/cargos por escuela. Hasta ahora el padrón era fixture (`escuelas.mock.json` + `catalogo-asignaturas.mock.json`).

## Decisión

Con `INTEGRATION_MODE=prod` (misma VPN/credenciales que ADR-009):

1. **Escuelas:** vista `VT_EMPRESAS_HABILITADA_INSCRIPCION` → `id` y `codigo_empresa` = `ID_EMPRESA` (p. ej. `EE0311230`), `nombre` = `N_EMPRESA`. **`cue`** se enriquece con LEFT JOIN lógico a `VM_PSX_EMPRESAS.CUE` (~6358 filas en lab; puede faltar en escuelas fuera del padrón PSX).
2. **Catálogo por escuela:** vista `VT_MATCAR_EMPRESA` → `ID_MATERIA_CARGO` / `N_MATERIA_CARGO`; `TIPO_TABLA` `M` = asignatura, `C` = cargo.
3. Adapter `OraclePadronAdapter` se precarga en `initIntegrations()` junto al alcance título.
4. En `mock` / `sql` / tests se mantiene `MockPadronAdapter`.
5. Los IDs demo `ESC-001` del seed **no** existen en lab; en prod la búsqueda Form C usa códigos `EE…` reales.

## Consecuencias

- Demo con VPN: Form C busca escuelas reales habilitadas (~1 100) y catálogo ministerial por empresa.
- CI no habla con Oracle.
- Auth, SGE, LOM, vocal y persistencia siguen mock. Mapa en prod: ADR-016.

## Referencias

- ADR-009 (alcance Oracle)  
- US-04 Formulario C  
- Lab MT4D EDUCAR  

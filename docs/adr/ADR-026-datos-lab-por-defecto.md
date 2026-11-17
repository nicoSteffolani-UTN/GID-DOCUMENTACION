# ADR-026: Datos del lab Oracle por defecto; mock como alternativa

**Estado:** Aceptado  
**Fecha:** 2026-10-07  
**Relacionado:** ADR-009 (alcance de título), ADR-015/016/019/020 (integraciones del lab), ADR-021 (Docker)

## Contexto

Hasta ahora `INTEGRATION_MODE` valía `mock` por defecto y el lab Oracle se activaba a mano (`.env` con `prod` en local, y un override aparte en Docker). Con alcance, padrón, catálogo, mapa, LOM y alertas ya integrados en solo lectura, el equipo trabaja y demuestra el portal con los datos del lab, y los fixtures quedaron como respaldo para cuando no hay VPN.

## Decisión

- `INTEGRATION_MODE` vale **`prod` por defecto** (`config/env.ts`, `.env.example`, `compose.yaml` y `compose.dev.yaml`).
- Los Compose pasan a la API las credenciales y la conexión del lab desde `src/.env` y montan el dump `ALCANCE_SQL_PATH` si está definido.
- **`mock` es la alternativa explícita:** `INTEGRATION_MODE=mock` en `src/.env` para `npm run dev:server`, o el override `compose.mock.yaml` en Docker.
- Los tests siguen siempre en `mock` (`jest.setup-env.js`), así que CI no necesita VPN.

## Consecuencias

**Positivas**

- Lo que se desarrolla y se demuestra es lo que muestra el lab, sin pasos extra.
- Un solo `src/.env` sirve para local y para Docker.

**Negativas**

- Sin VPN la API no arranca hasta elegir la alternativa `mock`; el error lo indica.
- Lo que sigue simulado aun en `prod` no cambia: login CiDi, SGE, asignación vocal–escuela, simulador, capacitaciones y la persistencia en JSON (ADR-003, ADR-017, ADR-018).

## Addendum (05/11/2026): sin modo de datos de ejemplo

Se quitan `mock` y `sql` como valores de `INTEGRATION_MODE`: la API arranca solo con `prod` (lab en vivo, VPN) o `foto` (copia del lab sin datos personales). Con `mock` o `sql`, la API no arranca y lo indica.

- **Por qué:** un sistema que se va a desplegar no debería poder arrancar con datos inventados. Sin los usuarios de ejemplo (addendum del 05/11 de ADR-031), `mock` mostraba escuelas ficticias a cuentas reales; para trabajar sin VPN, `foto` da datos reales.
- **Tests:** con `NODE_ENV=test`, `env.ts` fija el modo interno `test` sin mirar `INTEGRATION_MODE`, y `container.ts` usa los dobles de prueba de `src/integrations/mock/`. Sus datos pasan a `server/src/__tests__/fixtures/`, fuera de la imagen de Docker. En `src/fixtures/` quedan solo datos de referencia reales.
- **`sql`:** el dump de alcance sigue disponible en `prod` con `ALCANCE_SQL_PATH`, que ya tenía prioridad sobre `VT_MAT_GRADO_X_TITULO`.
- **`foto`:** lo que la copia no trae (antecedentes provinciales, antigüedad, puntaje LOM por docente) responde vacío en lugar de usar datos de ejemplo. Se elimina el chip «Datos de ejemplo» del legajo.
- **Mapa:** la capa base deja de ser el rectángulo de ejemplo y pasa a los límites departamentales de IDECOR.
- Se elimina `compose.mock.yaml`.

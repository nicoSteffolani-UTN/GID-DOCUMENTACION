# ADR-008: Mapas con Leaflet y GeoJSON

- **Estado:** Aceptado (prioridad P3 — fuera del MVP P1; spike PD-21 en repo)
- **Fecha:** 2026-05 (Instancia 2)
- **Actualizado:** 2026-10-07 (se quita el modo `idecor`, ver addendum)

## Contexto

Funcionalidades de mapas y oportunidades territoriales en documento de alcance; no forman parte del MVP académico P1. En julio 2026 se incorporó un spike Leaflet (PD-21) y, en paralelo, la capa WFS de IDECOR para escuelas estatales.

## Decisión

**Leaflet + dual mode de datos** en `/mapa`:

| `MAPA_MODE` | Fuente | Vacantes / interés |
|-------------|--------|--------------------|
| `mock` (default demo) | `GET /api/mapa/*` + fixture `mapa-establecimientos.mock.json` | Sí — semáforo urgencia, filtro asignatura, registrar interés (US-10 lite) |
| `idecor` | WFS IDECOR en el frontend | No — consulta territorial oficial |

Configuración:

- Backend: variable `MAPA_MODE` en [`src/.env.example`](../../src/.env.example) / `env.ts` (informativa / ops).
- Frontend: constante `MAPA_MODE` en `src/web/src/app/core/mapa/mapa-mode.config.ts` (elige el modo de la pantalla).

### Spike PD-21 (2026-07 / 2026-08)

- Fixture `src/fixtures/mapa-establecimientos.mock.json` (coordenadas demo Córdoba + vacantes mock).
- API `GET /api/mapa/establecimientos?asignatura=`, `GET /api/mapa/asignaturas`, `GET/POST /api/mapa/interes`.
- Pantalla `/mapa` (aspirante): tiles IGN, GeoJSON simplificado en modo mock, marcadores por urgencia, popup con vacantes e interés.
- Modo `idecor` conserva el trabajo de capa oficial (escuelas estatales secundarias) sin pisar el flujo demo académico.

Pendiente producción: GeoJSON oficial provincial, coordenadas reales del padrón, integración vacantes Ministerio, postulación completa US-10.

## Consecuencias

- **Positivo:** Demo académica ejecutable sin red IDECOR; IDECOR permanece disponible.
- **Negativo:** Dos fuentes de verdad de establecimientos hasta unificar padrón; el interés mock no es postulación oficial.

## Addendum 2026-10-07 — se quita el modo `idecor`

El modo `idecor` (WFS de escuelas consultado desde el navegador) era lento (~20 s, ~3 MB), inestable y no tenía
semáforo LOM ni registro de interés; desde ADR-019 el mapa del portal ya sale del lab Oracle. Se eliminan
`MAPA_MODE` (`env.ts`, Compose, `.env.example`, `/api/health`), `mapa-mode.config.ts`, el cliente WFS
(`core/mapa/mapa.service.ts`) y su popup. El mapa tiene una sola fuente: `GET /api/mapa/*`, que en
`INTEGRATION_MODE=prod` lee Oracle y en `mock` los fixtures.

**IDECOR se usa solo para una cosa:** los límites oficiales de los 26 departamentos (capa `idecor:departamentos`),
bajados una vez y versionados en `src/fixtures/departamentos-cordoba.geojson`. Con ellos el adapter Oracle asigna
el departamento a las ~98 sedes que el lab trae sin ese dato (ADR-019). No hay consultas a IDECOR en ejecución.

El crédito del mapa base cumple la licencia de OpenStreetMap (ODbL): "© colaboradores de OpenStreetMap · Escuelas:
Ministerio de Educación de Córdoba"; se quita el prefijo "Leaflet".

## Referencias

- Instancia 2 §11 · Instancia 1 (fuera de alcance inmediato) · US-10 lite

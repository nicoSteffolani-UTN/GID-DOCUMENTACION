# Demo local — Oracle lab MT4D (G24)

Guía operativa para usar el portal contra el laboratorio EDUCAR. **Es el modo por defecto (`INTEGRATION_MODE=prod`, ADR-026)**; los datos simulados (`mock`) quedan como alternativa sin VPN.

## Requisitos

1. VPN OpenVPN del lab conectada.
2. Copiar `src/.env.example` → `src/.env` y ajustar:

```env
INTEGRATION_MODE=prod
ORACLE_HOST=10.250.11.53
ORACLE_PORT=1521
ORACLE_SERVICE=mt4d
DB_USER=educar
DB_PASSWORD=<lab>
ORACLE_ALCANCE_OBJECT=VT_MAT_GRADO_X_TITULO
```

3. Arrancar API desde `src/server`. `initIntegrations()` precarga alcance, padrón, catálogo, mapa, LOM y notificaciones en memoria.

## Qué está integrado en lab (`prod`)

| Módulo | Fuente Oracle | Smoke |
|--------|---------------|-------|
| Alcance título (validador US-07) | `VT_MAT_GRADO_X_TITULO` (~74k) | `npx tsx src/integrations/alcance/smoke-oracle-alcance.ts` |
| Padrón + catálogo Form C | `VT_EMPRESAS_HABILITADA_INSCRIPCION` + `VT_MATCAR_EMPRESA` | `npx tsx src/integrations/padron/smoke-oracle-padron.ts` |
| Mapa vacantes + semáforo LOM | `T_PUBLICACIONES` (publicaciones JD, Interinato/Suplencia, **sin directivos** — ver ADR-016) + `GEDUC.VT_EMP_EMPRESAS_DOM` + `VT_MATERIAS_JUNTA` + `VT_MATCAR_EMPRESA`; promedios LOM secundaria: solo `T_DETALLES_INSC_JD`, un puntaje por docente (**históricos**, convocatorias 2017-2021 — ver ADR-019). `tuPuntaje` del semáforo: `VT_PSX_PUNTAJES_X_EMPLEADO`, con fallback demo `LomConRespaldoDemo` → `puntaje.mock.json` (CUIL demo sin valoración en lab) | `npx tsx src/integrations/mapa/smoke-oracle-mapa.ts` |
| Notificaciones de vacantes (PD-24) | `VT_PUBLI_EMPRE_MAT_ASPIRANTES` (vista de `T_PUBLICACIONES` + `T_PUBLICACIONES_ASPIRANTE`; activas y vigentes por fechas) | `npx tsx src/integrations/notificaciones/smoke-oracle-notificaciones.ts` |

### Nota lab — vacantes de la junta (ADR-020, actualizado 2026-09-28)

- Fuente de la campanita: `T_PUBLICACIONES` (cargos a cubrir) + `T_PUBLICACIONES_ASPIRANTE`,
  vía la vista `VT_PUBLI_EMPRE_MAT_ASPIRANTES` (trae escuela/asignatura/sitio de revista).
- "Postulable"/"vencida" se derivan de fechas: `ABIERTA` si está en rango,
  `PROGRAMADA` si aún no empezó, `FINALIZADA` si `FEC_HASTA_PUBLI < hoy`.
- Se muestran las publicaciones **del año en curso** (`FEC_DESDE_PUBLI` 2026).
  `T_PUBLICACIONES` tiene ~300k filas activas históricas (2011–2019) y un par de
  fechas corruptas (3013/4014); sin el filtro de año la campanita explotaría.
- `T_PSX_PUBLICACION` (modelo del portal, estados reales) quedó **descartado**
  como fuente de la campanita.
- Las alertas leen de **MT4T** por defecto (`ORACLE_NOTIF_CONNECT=10.250.11.60:1521/mt4t`);
  en MT4D no hay publicaciones del año en curso. No hace falta tocar el `.env`: se
  necesitan VPN, `DB_USER` y `DB_PASSWORD`. Con `ORACLE_NOTIF_CONNECT=` vacío se usa MT4D.

### Tiempo de arranque (`prod`)

`initIntegrations()` carga las fuentes Oracle en paralelo y el arranque dura lo que tarda la más lenta.
Medido el 2026-09-30 (por separado): alcance de títulos ~23 s, padrón y catálogo ~12 s, mapa/LOM ~2,5 s,
puntaje LOM ~0,6 s, alertas ~1 s. **La API queda escuchando en ~25 s** (antes ~100 s, cuando el promedio
LOM cruzaba `T_PUBLICACIONES` y `T_PUBLICACIONES_ASPIRANTE`; ADR-019). Si el arranque vuelve a tardar,
medir cada `connect()` por separado antes de tocar nada.

**01/10:** el cuello de botella era `fetchArraySize` (default 100 filas por viaje de red por VPN), no el SQL. Con `oracledb.fetchArraySize = 5000` en `initIntegrations()` el alcance baja de ~21 s a ~1,2 s, el catálogo de ~8 s a ~0,8 s y **`initIntegrations()` completo tarda ~2 s** (antes ~25 s).

## Qué sigue mock

- Login CiDi (usar mock-login demo)
- Antecedentes SGE / sync provincial
- Motor LOM desglosado (puntaje)
- Junta vocal (acta escuela)
- Persistencia legajo + Form C (`dev-store.json`)
- Simulador ascenso y capacitaciones

## Chequeos rápidos

| Chequeo | Esperado |
|---------|----------|
| `GET /api/health` | `integrationMode: prod`, bloques `alcance`, `padron`, `mapa` y `notificaciones` con `ready: true` |
| Login mock | CUIL `20123456789` / `demo1234` |
| Validador | Título `PROFESOR DE COMPUTACION Y MATEMATICA` habilita código `320` |
| Form C | Buscar escuela `EE…` (no existe `ESC-001` del seed) |
| Mapa | Filtro por grado Meta4 (ej. `13105`), no por código ministerial `320` |

## Con Docker

Con la VPN conectada en el host y el mismo `src/.env`, los Compose ya usan el lab:

```bash
cd src
docker compose up -d --build --wait
```

El portal queda en http://localhost:8080. Comandos para manejarlo en [`src/README.md` → Guía rápida](../src/README.md#guía-rápida); detalle en [`src/README.md` → Datos del lab y alternativa mock](../src/README.md#datos-del-lab-y-alternativa-mock) y [ADR-021](./adr/ADR-021-contenedores-docker.md).

## Sin VPN

`INTEGRATION_MODE=prod` falla al arrancar con mensaje accionable. Alternativas: `INTEGRATION_MODE=mock` en `src/.env` (o `compose.mock.yaml` en Docker), o `sql` con el dump `ALCANCE_SQL_PATH`.

## Referencias

- [ADR-009](./adr/ADR-009-integracion-alcance-titulo.md) · [ADR-015](./adr/ADR-015-padron-oracle-lab.md) · [ADR-016](./adr/ADR-016-mapa-vacantes-oracle-lab.md) · [ADR-020](./adr/ADR-020-alertas-vacantes-mock.md)
- [README server](../src/server/README.md)

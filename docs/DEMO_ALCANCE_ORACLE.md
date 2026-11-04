# Demo local — alcance título con Oracle lab

Guía operativa para demostrar el **validador US-07** contra el laboratorio MT4D (Gobierno de Córdoba). Ver también [DEMO_ORACLE_LAB.md](./DEMO_ORACLE_LAB.md) para el estado completo de integraciones lab.

## Requisitos

1. VPN OpenVPN del lab conectada.
2. Variables en `src/.env` (no versionar secretos):

```env
INTEGRATION_MODE=prod
ORACLE_HOST=10.250.11.53
ORACLE_PORT=1521
ORACLE_SERVICE=mt4d
ORACLE_USER=educar
ORACLE_PASSWORD=<lab>
DB_USER=educar
DB_PASSWORD=<lab>
ORACLE_ALCANCE_OBJECT=VT_MAT_GRADO_X_TITULO
```

Alternativa TNS: `TNS_ADMIN` + `DB_ALIAS=MT4D` (requiere `tnsnames.ora` y, si aplica, entradas en el archivo hosts).

3. Arranque API desde `src/server` (o copia local de trabajo). Al iniciar, `initIntegrations()` carga la vista y deja el mapa en memoria.

## Qué verificar

| Chequeo | Esperado |
|---------|----------|
| `GET /api/health` | `integrationMode: "prod"`, `alcance.ready: true`, `filasCargadas` ~74 000, `sourceObject: VT_MAT_GRADO_X_TITULO` |
| Login mock | CUIL `20123456789` / `demo1234` |
| Legajo | Título demo `PROFESOR DE COMPUTACION Y MATEMATICA` (alineado a lab; habilita `320`) |
| Formulario C | Inscripción a Matemática (`320`) habilitada; asignatura no cubierta por el título → mensaje de rechazo con código |
| Smoke CLI | `npx tsx src/integrations/alcance/smoke-oracle-alcance.ts` |

**Competencia docente / habilitante / supletorio:** `VT_MAT_GRADO_X_TITULO` no la trae. Para tenerla, definí además
`ALCANCE_SQL_PATH` con la ruta local al dump `TITULOS_ALCANCES` (barras `/`, sin comillas): el alcance sale del
archivo y el resto sigue en el lab. `/api/health` muestra `sourceObject: "ALCANCE_SQL_PATH"`. Detalle en
[ADR-009, addendum 2026-10-02](./adr/ADR-009-integracion-alcance-titulo.md).

Sin VPN, `INTEGRATION_MODE=prod` debe fallar el arranque con mensaje accionable. Usar `mock` o `sql` (dump `TITULOS_ALCANCES` vía `ALCANCE_SQL_PATH`).

## Límites (no chamuyo)

- **Integrado a lab:** solo alcance título.
- **No integrado:** autenticación CiDi, antecedentes SGE, padrón escuelas, motor LOM, persistencia Oracle de legajo/Form C.
- La tabla export `TITULOS_ALCANCES` del Drive **no** existe en el esquema lab EDUCAR; la fuente viva es `VT_MAT_GRADO_X_TITULO`.

## Referencias

- [ADR-009](./adr/ADR-009-integracion-alcance-titulo.md)
- [README server — integraciones](../src/server/README.md)
- [Trazabilidad Sprint P2](./TRAZABILIDAD_SPRINT_P2.md) (guión UI; alcance lab es complemento P1)

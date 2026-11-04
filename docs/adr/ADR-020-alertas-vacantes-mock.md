# ADR-020: Alertas de vacantes (PD-24)

**Estado:** Aceptado (fuente actualizada: `T_PUBLICACIONES` vía `VT_PUBLI_EMPRE_MAT_ASPIRANTES` · mock por defecto)  
**Actualizado:** 2026-09-28

## Contexto

El portal necesita consumir las publicaciones de vacantes que abre la junta y
que el aspirante marque las que le interesan (campanita con contador de no
leídas + listado filtrable por "mis escuelas"). La duda histórica era si las
publicaciones del lab "están abiertas". La respuesta llegó validando el esquema
en vivo (2026-09-14/15): las publicaciones **vivas** del portal están en
`T_PSX_PUBLICACION` con su **estado real** (diccionario `T_PSX_ESTADO_PUBLICACION`)
y datos vigentes 2026 — sí hay vacantes abiertas. `T_PUBLICACIONES` (2012–2019,
legacy) quedó descartada.

## Decisión

1. **Adapter `OracleNotificacionesAdapter`** (implementa `NotificacionesVacantesPort`),
   precargado en `initIntegrations()` junto a alcance/padrón/mapa/LOM cuando
   `INTEGRATION_MODE=prod`:
   - `T_PSX_PUBLICACION p` → publicación + `ID_ESTADO_PUBLICACION` + fechas del
     ciclo (`FECHA_PUBLICACION`, `FECHA_CIERRE`).
   - `VT_PUBLI_EMPRE_MAT_ASPIRANTES v` (LEFT JOIN por `ID_PUBLICACION`) → detalle
     legible: `ESCUELA` (código + nombre), `ASIGNATURA_O_CARGO` (código + nombre),
     `SIT_REVISTA`. Hasta 2 filas por publicación → una vacante por cargo.
   - En `mock`/`sql`/tests se mantiene `MockNotificacionesAdapter` con
     `fixtures/notificaciones-vacantes.mock.json` y **fechas relativas**
     (`desdeEnDias`/`cierreEnDias`) para que el demo siempre tenga abiertas.
2. **"Abierta" = estado real de la junta**, no fechas. Diccionario
   `T_PSX_ESTADO_PUBLICACION` validado en vivo: `3 = PROGRAMADA`,
   `4 = ABIERTA A POSTULACIONES` (postulables); resto cerradas
   (EN SELECCIÓN, CUBIERTA, FINALIZADA, CANCELADA, etc.). El SQL filtra
   `ID_ESTADO_PUBLICACION IN (3, 4)`.
3. **`T_PUBLICACIONES` descartada**: legacy sin datos desde 2019 y una fila
   anómala con `FEC_HASTA_PUBLI=3013`; sus fechas hacían derivar todo como
   FINALIZADA y no reflejaba el estado real de la junta.
4. **"Mis escuelas"** = intereses del mapa ∪ escuelas del Form C del usuario;
   `?soloMisEscuelas=true` filtra `abierta && esDeMisEscuelas`.
5. **"Leída"** persiste en el store local por CUIL (`dev-store.json`), como los
   intereses de mapa. La bandeja `T_PSX_NOTIFICACION_USUARIO` / alertas propias
   del portal (`T_PSX_ALERTA_PUBLICACION`, `T_PSX_ALERTAS_USUARIOS`) queda como
   posible mejora cuando se valide el modelo de bandeja.
6. **Módulo `perfil`:** `GET /api/perfil` consolida puntaje LOM, legajo,
   inscripción (Form C), intereses de mapa y resumen de notificaciones.
7. **Postulación (Fase 2):** `T_PSX_POSTULACION` no tiene fecha; los eventos
   viven en `T_PSX_MOVIMIENTO_POSTULACION`. Requiere validación en el lab antes
   de implementar.

## Consecuencias

- En `INTEGRATION_MODE=prod` la campanita lista las publicaciones postulables
  reales de la junta (lab al 2026-09-15: 6 vacantes, 3 "ABIERTA A POSTULACIONES"
  y 3 "PROGRAMADA"); el demo con fechas relativas lo sigue aportando el mock.
- `GET /api/health` expone bloque `notificaciones` (filas + fuentes + estado).
- Smoke: `npx tsx src/integrations/notificaciones/smoke-oracle-notificaciones.ts`.
- Mapa de columnas del detalle (`ESCUELA`→`splitCodigoNombre`, horas/turno/
  curso no disponibles en la vista → `0`/`Sin turno`) documentado en el adapter.

### Pendiente / próximos pasos

- **Orden de la lista en prod**: hoy ordena por `FECHA_CIERRE` ascendente.
  Decisión postergada (2026-09-15): evaluar mostrar primero las `ABIERTA` y
  luego las `PROGRAMADA`.
- Incorporar la bandeja real `T_PSX_NOTIFICACION_USUARIO` / alertas del portal
  (`T_PSX_ALERTA_PUBLICACION`, `T_PSX_ALERTAS_USUARIOS`) cuando se valide su modelo.

## Actualización (2026-09-28): fuente = publicaciones de la JD

El área que conoce el modelo indicó que las tablas de publicación de cargos a
cubrir son `T_PUBLICACIONES` + `T_PUBLICACIONES_ASPIRANTE`. La campanita pasa a
consumirlas vía la vista `VT_PUBLI_EMPRE_MAT_ASPIRANTES` (que trae escuela,
asignatura/cargo y sitio de revista).

- Se abandona `T_PSX_PUBLICACION` (+ `T_PSX_ESTADO_PUBLICACION`) como fuente.
- Como `T_PUBLICACIONES` no tiene columna de estado, se muestran las
  publicaciones **del año en curso** (`EXTRACT(YEAR FROM FEC_DESDE_PUBLI)`) y
  `abierta` se deriva de fechas: `ABIERTA` si está en rango, `PROGRAMADA` si aún
  no empezó, `FINALIZADA` (vencida) si `FEC_HASTA_PUBLI < hoy`. Las vencidas se
  listan aparte, marcadas.
- Motivo: sin filtro, `T_PUBLICACIONES` tiene ~300k filas activas históricas
  (2011–2019) más fechas corruptas (3013/4014); con el año en curso queda un
  conjunto chico (en el lab, 0; en test, 2 vencidas).
- "Leída" y "eliminada" persisten en el store local por CUIL. Endpoints:
  `POST /api/notificaciones/:id/leida` (marcar),
  `DELETE /api/notificaciones/:id/leida` (desmarcar) y
  `DELETE /api/notificaciones/:id` (eliminar de la bandeja).
- Localidad de la escuela: `LEFT JOIN` con `GEDUC.VT_EMP_EMPRESAS_DOM`
  (`ORACLE_NOTIF_LOCALIDAD_OBJECT`, `N_LOCALIDAD`) matcheando el código extraído
  de `ESCUELA` (`SUBSTR(... INSTR(ESCUELA, ' - '))`).
- `ORACLE_NOTIF_PUBLICACIONES_OBJECT` (default `VT_PUBLI_EMPRE_MAT_ASPIRANTES`);
  se eliminó `ORACLE_NOTIF_DETALLE_OBJECT`.
- Localidad/barrio: `LEFT JOIN` a `GEDUC.VT_EMP_EMPRESAS_DOM`
  (`ORACLE_NOTIF_LOCALIDAD_OBJECT`), agrupado por `ID_EMPRESA` (`MAX`) para no
  duplicar cuando la vista trae más de una fila por escuela. Horas/turno/curso
  se completan desde `T_PUBLICACIONES` + `T_PSX_TURNOS_CONVOCATORIAS_TTT`.
- `ORACLE_NOTIF_CONNECT` permite que **sólo las alertas** lean de otra base mientras el
  resto del portal sigue en el lab general (MT4D). Desde 2026-09-30 su **default es MT4T**
  (`10.250.11.60:1521/mt4t`): la misma consulta devuelve 4 publicaciones del año en MT4T
  y 0 en MT4D, por lo que antes las alertas solo se veían en PC con el override en su
  `.env` local. Con `ORACLE_NOTIF_CONNECT=` vacío se vuelve a MT4D.

Pendiente: revisar el mapa, que también usa `T_PUBLICACIONES` para vacantes
históricas, y decidir si debe ocultarlas.

## Referencias

- ADR-016 (mapa vacantes, publicaciones JD) · ADR-018 (vocal mock pendiente lab)
- PD-24 · Lab MT4D EDUCAR: `T_PSX_PUBLICACION`, `T_PSX_ESTADO_PUBLICACION`,
  `VT_PUBLI_EMPRE_MAT_ASPIRANTES` (todas validadas en vivo)
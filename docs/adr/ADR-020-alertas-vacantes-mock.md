# ADR-020: Alertas de vacantes (PD-24)

**Estado:** Aceptado (fuente: `T_PUBLICACIONES` directo desde el addendum del 14/11; desde ADR-026 el lab es el default y el mock, la alternativa)  
**Actualizado:** 2026-11-14

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
  (`<host-de-MT4T>:1521/mt4t`): la misma consulta devuelve 4 publicaciones del año en MT4T
  y 0 en MT4D, por lo que antes las alertas solo se veían en PC con el override en su
  `.env` local. Con `ORACLE_NOTIF_CONNECT=` vacío se vuelve a MT4D.

Pendiente: revisar el mapa, que también usa `T_PUBLICACIONES` para vacantes
históricas, y decidir si debe ocultarlas.

## Addendum (14/11/2026): consulta como el sistema original y alertas solo de primera y única

**Contexto.** La Junta confirmó (reunión del 13/11) que la «Consulta de vacantes» del sistema original (CiDi → Junta de
Clasificación Secundaria → Gestionar Vacantes) no tiene botón de postular ni de inscribirse: solo muestra los datos, y la
inscripción a la vacante es presencial en la escuela. Además, en el año puede haber ~2.000 vacantes. Tipos de
convocatoria (Instructivo Administrativo 2025 de nivel secundario):

| `NRO_PUBLICACION` | Convocatoria | Quién se puede inscribir |
|---|---|---|
| 1 | Primera convocatoria (titularidades) | Los de la LOM de esa escuela (más reubicación, concentración, acrecentamiento, traslados, cambio de cátedra y reingreso, con prioridad) |
| 2 | Segunda convocatoria (titularidades) | Si la primera no se cubre: todos los inscriptos en la convocatoria, aunque no estén en esa escuela |
| 3 | Convocatoria única (suplencias y algunos interinatos) | Cualquiera; prioridad para quien ya trabaja en esa escuela |

Cada vacante se publica 3 días hábiles y la escuela recibe la inscripción hasta la primera hora hábil del día siguiente
al cierre. Lo que no se cubre se designa por fuera del padrón y no aparece en el sistema.

**Decisión.**

1. **Fuente: `EDUCAR.T_PUBLICACIONES` directo** (MT4T), una fila por vacante. Se deja la vista
   `VT_PUBLI_EMPRE_MAT_ASPIRANTES`, que arma una columna con nombre y DNI de los postulantes. Nunca se leen
   `T_PUBLICACIONES_ASPIRANTE` ni columnas de usuario; el adaptador rechaza esas fuentes aunque se configuren en
   `ORACLE_NOTIF_PUBLICACIONES_OBJECT` (default nuevo: `EDUCAR.T_PUBLICACIONES`).
2. **Filtro:** año en curso, `FEC_BAJA_LOGICA IS NULL`, secundario (`ID_TIPO_NIVEL` '2' o '02'),
   `NVL(PERTENECE_PIT,'N') <> 'S'` (el PIT está fuera de alcance) y `NRO_PUBLICACION IN (1,2,3)` (los nulos no se
   muestran). Nombres con el criterio de la vista: escuela de `VT_EMPRESAS_GEDUC_PUBL_X_NIV`; materia o cargo de
   `T_MATERIAS_JUNTA` ∪ `T_GRADOS_JUNTA` (agrupamiento 08, nivel 13, nivel Junta 2, `ACTIVO_DESIGNACION` S o D).
   Departamento, localidad y barrio de `GEDUC.VT_EMP_EMPRESAS_DOM`; turno de `T_PSX_TURNOS_CONVOCATORIAS_TTT`
   ('1' y '02' se comparan por número); horarios de `T_PUBLICACIONES_HORARIO` («Miércoles 12:40 a 15:00», de lunes a
   domingo). La observación (dirección y horario de presentación) va tal como viene.
3. **Consulta filtrada y paginada en el servidor.** `GET /api/notificaciones/vacantes` recibe escuela, departamento,
   localidad, situación de revista, convocatoria y asignatura/cargo, más página y tamaño (20 por defecto, máximo 100), y
   devuelve el total y los valores de los filtros. Nunca se mandan todas las vacantes al front.
4. **Vista por defecto: las escuelas del Formulario C.** Desde ahí se cambia a «Todas las escuelas». Sin Formulario C
   arranca en todas, con un aviso.
5. **Alertas (campanita y «Mis alertas»): solo primera convocatoria y única.** La segunda se ve en la consulta pero no avisa:
   la busca el docente. Una vacante es alerta si está abierta, es de primera o única, cumple la situación de revista y la
   convocatoria elegidas (si eligió) y coincide con cualquiera de sus intereses (escuela, materia o cargo, departamento,
   localidad o barrio). Sin nada programado no hay alertas; antes la campanita contaba todas las abiertas.
6. **Programar las alertas** reusa las preferencias de aviso: se suman departamentos, situación de revista y
   convocatoria (primera o única), y se pueden pausar y reanudar sin perder lo elegido. Migración `017_aviso_programado.sql`
   (017 porque la 015 y la 016 son del historial de revisión).
7. **Sin botones de acción sobre la vacante:** se saca «Agregar a mi inscripción anual». Quedan «Ver en el mapa» y, en
   las alertas, «Marcar como leída» y «Eliminar».
8. **Foto del lab:** guarda las filas con convocatoria, departamento, horarios y observación (datos de escuelas). Una
   foto tomada antes no trae la convocatoria y no muestra vacantes: hay que volver a tomarla.

**Consecuencias.**

- Smoke: conteos por convocatoria y situación de revista, y cuántas se excluyen por PIT o sin convocatoria. En MT4T al
  14/11: 14 publicaciones de secundario del año, 5 del PIT, 9 se muestran (5 de primera, 4 únicas, ninguna de segunda).
- Duda abierta: «programar» se tomó como elegir qué avisar y pausar. Si la Junta quería un horario o una frecuencia de
  aviso (p. ej. un resumen diario), queda para otra iteración.

## Referencias

- ADR-016 (mapa vacantes, publicaciones JD) · ADR-018 (vocal mock pendiente lab)
- PD-24 · Lab MT4D EDUCAR: `T_PSX_PUBLICACION`, `T_PSX_ESTADO_PUBLICACION`,
  `VT_PUBLI_EMPRE_MAT_ASPIRANTES` (todas validadas en vivo)
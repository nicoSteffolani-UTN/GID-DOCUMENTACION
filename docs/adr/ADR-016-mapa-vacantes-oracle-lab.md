# ADR-016: Mapa vacantes desde Oracle lab

**Estado:** Aceptado (MVP lab)  
**Actualizado:** 2026-09-12 (vacantes desde publicaciones JD; se descarta CO como origen del filtro)

## Contexto

PD-21 expone `GET /api/mapa/*` con fixture `mapa-establecimientos.mock.json` (coordenadas demo + vacantes por código ministerial, p. ej. `320` Matemática, con situación Interinato/Suplencia). Con VPN al lab MT4D la primera fuente probada para vacantes fue `T_CO_VACANTES`, pero ese catálogo corresponde al **Concurso de Oposición (cargos de conducción)**: todos los puestos que expone son DIRECTOR/VICEDIRECTOR/REGENTE (grado Meta4 `131xx`/`132xx`), que **no** son asignaturas de horas/cátedra. El filtro "Asignatura / cargo" del mapa traía directivos → se descartó.

## Decisión

Con `INTEGRATION_MODE=prod`:

1. **Vacantes/asignaturas/cargos:** publicaciones de la **Junta Docente** vía `T_PUBLICACIONES`, sólo `ID_SIT_REVISTA` `03` (Interinato) y `05` (Suplencia), activas (`FEC_BAJA_LOGICA IS NULL`) y **sin cargos directivos** (`SUBSTR(GRADO_MATERIA,1,1) NOT IN ('D','J','R')`). Cada fila se agrega como `COUNT(ID_PUBLICACION)` por (escuela, grado, situación de revista) y alimenta el semáforo de urgencia y el contador "N publicaciones" del popup.
2. **Georreferencia + datos de la escuela:** vista `GEDUC.VT_EMP_EMPRESAS_DOM` (misma que la capa de escuelas LOM, filtro `N_NIVEL_EDUCATIVO='SECUNDARIO'`, `PRIVADO='N'` o `PRIVADO` nulo con `DEPENDENCIA='PROVINCIAL'`, con coordenadas).
3. **Nombre de la materia:** `VT_MATERIAS_JUNTA` (`ID_MATERIA` → `N_MATERIA`); respaldo para cargos docentes `VT_MATCAR_EMPRESA` (`ID_MATERIA_CARGO` → `N_MATERIA_CARGO`, `TIPO_TABLA='C'`). Cobertura real: 906 de 1.036 códigos por materias + 85 por cargos (≈95 %).
4. Adapter `OracleMapaAdapter` precargado en `initIntegrations()` junto a alcance y padrón.
5. **GeoJSON provincial:** sigue el fixture demo (Oracle lab no provee polígono provincial en estas fuentes).
6. En `mock` / `sql` / tests se mantiene `MockMapaAdapter`.

## Notas

- `T_CO_VACANTES` queda **descartado** como origen del filtro/vacantes del mapa; sólo expone cargos de conducción. No se elimina su objeto; simplemente no se consulta.
- El conteo por (escuela, grado, revista): `T_PUBLICACIONES` tiene ~188 mil filas activas 03/05 sobre ~1.260 escuelas georreferenciadas; la consulta agregada corre ~8 s en lab y se precarga al iniciar.
- La etiqueta "Interinato/Suplencia" sale de `ID_SIT_REVISTA` 03/05 (no hay vista de descripción en el set consultado).

## Limitaciones lab (documentar en demo)

- El mapa de vacantes es **histórico** igual que la LOM: se construye sobre publicaciones de la convocatoria VA 2018 ("VACANTES APEX"); no refleja la convocatoria vigente.
- ~5 % de los códigos de `GRADO_MATERIA` no resuelven nombre en los catálogos consultados; se muestra el código tal cual.
- Urgencia (semáforo): heurística por cantidad de publicaciones por escuela hasta confirmar regla ministerial.

## Consecuencias

- Demo con VPN: mapa con vacantes de asignaturas/cargos docentes reales (sin directivos), escuelas secundarias estatales georreferenciadas y promedios LOM históricos.
- CI no habla con Oracle.
- Auth, SGE, LOM, vocal y persistencia siguen mock.

## Actualización (2026-09-28): el mapa ya no muestra vacantes

El mapa dejó de exponer las publicaciones/vacantes por materia. Ahora muestra **escuelas con el
semáforo LOM** (ver ADR-019) y el **interés se registra por escuela**, no por materia. Se
eliminaron del adapter/API el filtro de asignatura y las vacantes; las publicaciones vigentes
se consumen en las **alertas de vacantes** (ADR-020). Este ADR queda como registro del
relevamiento de `T_PUBLICACIONES`, ya no aplicado al mapa.

## Referencias

- ADR-008 (mapa Leaflet) · ADR-015 (padrón Oracle) · ADR-019 (LOM secundaria semáforo)
- PD-21 · Lab MT4D EDUCAR
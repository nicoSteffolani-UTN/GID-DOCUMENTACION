# ADR-030: Antigüedad docente y cargos vigentes en el perfil

**Estado:** Aceptado  
**Fecha:** 2026-10-07  
**Relacionado:** ADR-010 (motor LOM), ADR-017 (antecedentes provinciales), ADR-021 (despliegue)

## Contexto

El docente quiere ver en su perfil cuánta antigüedad tiene y cuánto tiempo lleva en cada escuela donde trabaja. El DBA del
Ministerio indicó la vista `PEOPLENET.M4CBA_VW_CONC_DIR` (Meta4/PeopleNet), accesible desde el lab MT4D. Relevamiento (07/10):

- Un registro por **cargo vigente hoy** (233.631 filas, 71.224 personas): solo situación de revista `01` (permanente) y `05`
  (interino). No hay historial: las escuelas donde el docente ya no trabaja no aparecen.
- `DT_START`: alta en el cargo. `ID_EMPRESA`: escuela (mismo código que `GEDUC.VT_EMP_EMPRESAS_DOM`; 408 de 411 escuelas de Media
  resuelven nombre). `ID_GRADO` → nombre en `PEOPLENET.M4_GRADOS`.
- `ANTIG_DOCENTE`: antigüedad docente total, calculada por la función `M4CBA_CANTIG` como **texto**
  (`"Doc.Publ. :29 Años. 02 Meses. 06 Dias. "`), una por relación laboral.
- **No tiene CUIL.** `SCO_ID_HR` = dígito de sexo (1/2) + DNI de 8 dígitos (verificado en 20.000/20.000 filas de
  `VT_AP_DESIGNADOS`, que trae `SCO_ID_HR`, `SEXO` y `DNI`). El DNI se obtiene del CUIL (posiciones 3 a 10).
- Incluye todos los niveles y la enseñanza privada.

## Decisión

- Nuevo puerto `AntiguedadDocentePort.getCargosVigentes(cuil)`, **asíncrono** y consultado **por request**
  (`WHERE SCO_ID_HR IN ('1'||:dni, '2'||:dni)`, ~0,1 s de consulta, 0,6–2 s con la conexión por VPN). No se precarga como los
  demás adapters porque `M4CBA_CANTIG` se evalúa por fila y la vista completa es cara.
- `GET /api/perfil` agrega `antiguedad`: total (la mayor informada) y las escuelas agrupadas, con el tiempo desde el alta más antigua
  y la lista de cargos. Si el lab falla, el perfil se muestra igual con `disponible: false`.
- `mock`/test: `fixtures/antiguedad.mock.json`. En `prod` solo el lab: si el CUIL no figura, el perfil muestra que no hay
  cargos vigentes (sin datos de ejemplo, igual que el puntaje desde el #66). Objeto configurable con `ORACLE_ANTIGUEDAD_OBJECT`.
- Se muestran todos los cargos vigentes, sin filtrar nivel ni sector: es información del propio docente, no entra en la LOM.

## Alternativas

- **Precargar la vista al iniciar**: descartada por el costo de `M4CBA_CANTIG` sobre 233k filas.
- **Cruzar CUIL por una tabla de personas**: `M4CBA_VW_CONSE_CAR` trae `CBA_CUIT`, pero tarda > 100 s y no coincidió en la muestra.
- **Calcular la antigüedad sumando cargos**: descartada; la calcula Meta4 con sus reglas (licencias, superposición).

## Consecuencias

- (+) Dato oficial y actualizado al día, sin carga del docente.
- (−) Solo cargos vigentes: no hay historial de escuelas anteriores.
- (−) Depende de la regla `SCO_ID_HR` = sexo + DNI; si Meta4 cambia la numeración hay que relevar de nuevo.
- (−) Una conexión Oracle por consulta del perfil; si se vuelve lento, usar un pool.
- (−) El texto de `ANTIG_DOCENTE` se parsea con una expresión regular; si no coincide, el total queda vacío.

## Addendum 2026-10-07 — trayectoria (escuelas actuales y anteriores)

**Contexto.** El docente también quiere ver dónde trabajó antes, y `M4CBA_VW_CONC_DIR` solo trae cargos vigentes. Relevamiento
en el lab (smoke por `SCO_ID_HR` sobre vistas con columnas de fecha de fin):

- `PEOPLENET.M4_HIST_CARGOS_EMP`: rápida (~70 ms), pero `ID_CENTRO_TRABAJO` vacío, `ID_CARGO` es un código de plaza que no
  resuelve en `M4_GRADOS` y cada movimiento aparece repetido. Descartada.
- `EDUCAR.VT_CONTANCIAS_SERVICIO`: mismos períodos que la siguiente, sin código de escuela separado (~2,9 s). Descartada.
- **`PEOPLENET.M4CBA_VW_CONSE_CAR`**: un registro por período de cada cargo (designación, traslado, titularización…), vigentes
  y terminados (`DT_END` vacío o `4000-01-01` = vigente). Por `SCO_ID_HR` tarda **87 ms de mediana** (30 docentes de Media;
  máx. 1 s). Lo de "> 100 s" de las alternativas de arriba era buscando por `CBA_CUIT`. `CENTRO_TRAB` trae
  `"<código> <código> <nombre>"`: el primer código coincide con `ID_EMPRESA` de `GEDUC.VT_EMP_EMPRESAS_DOM` en 1.200 de 1.210
  cargos. Mezcla otros tipos de fila (`Zona`, `Ausentismos`, `Reconocimiento de Servicios`): se filtra `TIPO = 'Cargos'`.

**Decisión.**

- `AntiguedadDocentePort.getHistorialCargos(cuil)` en el mismo adapter (misma clave por DNI, consulta por request, en paralelo
  con los cargos vigentes). Objeto configurable con `ORACLE_HISTORIAL_OBJECT`.
- `antiguedad.escuelas` pasa a ser la **trayectoria**: vigentes + historial agrupados por escuela, con `desde` (alta más
  antigua) y `hasta` (baja más reciente, `null` si hoy trabaja ahí). Actuales primero; después las anteriores, de la última
  baja a la primera. Un período con baja futura cuenta como actual.
- Escuela actual: se muestran sus cargos de `CONC_DIR` (situación de revista de hoy). Escuela anterior: cada cargo una sola vez.
- Si falla solo el historial, el perfil muestra igual los cargos vigentes.
- El fixture `antiguedad.mock.json` agrega `historial` (dos escuelas anteriores sintéticas para el CUIL demo).

**Consecuencias.**

- (+) Desaparece la consecuencia negativa "solo cargos vigentes".
- (−) Dos conexiones Oracle por consulta del perfil.
- (−) La duración por escuela va de la primera alta a la última baja sin descontar huecos; la antigüedad oficial sigue siendo
  el total de Meta4.
- (−) Algunas escuelas viejas o de otros niveles no tienen nombre en `CENTRO_TRAB` ni en el padrón: se muestra el código.

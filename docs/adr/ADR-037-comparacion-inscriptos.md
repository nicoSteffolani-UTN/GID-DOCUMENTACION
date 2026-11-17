# ADR-037: Comparación con los inscriptos en «Mi puntaje»

**Estado:** Aceptado
**Fecha:** 2026-11-13
**Relacionado:** historia «Visualizar gráficos de competitividad» (US-16), ADR-019 (semáforo LOM del mapa), ADR-027 (puntaje estimado), ADR-026 (modo foto)

## Contexto

La historia pide que el docente vea cómo está su puntaje frente al resto de los inscriptos. El equipo decidió (11/11) que no es una pantalla nueva ni va en la inscripción ni en la carga de antecedentes, que tienen que quedar parecidas al sistema original: va solo dentro de «Mi puntaje».

El lab tiene los puntajes de la Junta en `T_DETALLES_INSC_JD` para 2017, 2018 y 2019 (~53 a ~59 mil docentes por año), una carga chica en 2020/2021 (~1.150, parece copia) y 3 filas de prueba en 2024. En 2019 el promedio baja de ~38 a ~25,7, posiblemente por un cambio de escala.

## Decisión

1. **Fuente y criterios del mapa (ADR-019).** Se usa `T_DETALLES_INSC_JD` con el mismo filtro que el mapa (`filtroDetallesLom`: Provincia, sin baja, `ID_REFERENCIA = 0`, sin cargos directivos), un solo puntaje por docente (el mejor) y el campo `PUNTAJE`. El departamento sale de `GEDUC.VT_EMP_EMPRESAS_DOM` (escuela madre) con las mismas correcciones de ubicación del mapa.
2. **Grupo = materia o cargo × departamento.** El código es `NVL(ID_MATERIA, ID_GRADO_JUNTA)`, el mismo que usa el catálogo de la inscripción (`itemCodigo`). El departamento de cada fila del docente es el de su escuela en el mapa.
3. **Convocatoria: la más reciente con datos, por grupo.** Igual que el mapa por escuela: para cada materia y departamento se toma la convocatoria más reciente que tenga al menos 10 docentes. Así, cuando el lab tenga convocatorias nuevas con datos, se usan solas, sin cambiar código. Con el lab de hoy, 4.125 grupos salen de 2019 y el resto de 2017, 2018 o 2021. La convocatoria viaja en la respuesta (`comparacion.convocatoria`) pero no se muestra al docente.
4. **Una sola convocatoria por grupo, nunca evolución entre años.** Por el posible cambio de escala de 2019, comparar años diferentes confundiría más de lo que aclara.
5. **Agregado en Oracle, una vez al arrancar.** Una consulta con `PERCENTILE_CONT` (p10, p25, p50, p75, p90) y un histograma en rangos fijos de 10 puntos hasta «120 o más» (en el lab el p90 llega a ~117). Ninguna fila por docente llega a Node. Tarda ~10 s y queda en memoria, como el mapa.
6. **Anonimato (k ≥ 10).** Un grupo con menos de 10 docentes no se muestra: la respuesta dice solo `suficiente: false`, sin la cantidad. El corte está en el SQL, en el mapeo de filas y en el servicio.
7. **En la respuesta del puntaje.** `GET /api/puntaje/inscripcion` suma `comparacion` a cada fila (solo para el aspirante; el vocal recibe 403). La posición del docente («por encima de X de cada 10») se calcula con su estimado de esa fila, interpolando entre los percentiles: por debajo del p10 da 0 y por encima del p90 da 9, nunca «todos». Nunca se devuelve el puntaje de otra persona.
8. **Pantalla.** En «Mi puntaje», una línea por materia («En Capital, la mitad de los inscriptos en Matemática tenía menos de 32 puntos. Tu estimado (45) está por encima de 6 de cada 10.») y un desplegable con el histograma (SVG en el template, sin librerías) o la tabla de percentiles. Un aviso dice que es una comparación con datos históricos, que el puntaje es estimado y que el orden de mérito oficial lo publica la Junta. No se habla de «chances» ni de «probabilidad».
9. **Modo foto.** La foto del lab (`npm run lab:foto`) guarda los grupos ya agregados. Una foto anterior a este cambio sigue sirviendo: «Mi puntaje» no muestra la sección hasta que se tome una nueva.

## Alternativas descartadas

- **Pantalla o endpoint aparte:** suma una llamada y una pantalla que el equipo no quería; la respuesta del puntaje ya tiene las filas.
- **Una sola convocatoria para todos los grupos (la de más volumen):** obliga a cambiar el umbral cuando llegue una convocatoria nueva y no se comporta como el mapa.
- **Chart.js o ECharts:** el SVG alcanza para barras y una línea, y no suma dependencias antes de pasar a Angular 21.

## Consecuencias

- Con el lab de hoy, la mitad de los ítems del catálogo tiene grupo para comparar. Los que no, son sobre todo materias de 4 dígitos de los planes nuevos, sin inscriptos en el lab: muestran «No hay suficientes inscriptos para comparar».
- Grupos de distintas materias pueden salir de convocatorias distintas (y de escalas distintas, si se confirma el cambio de 2019).
- **Pendiente con la DBA:** cuál es el puntaje definitivo, `PUNTAJE` o `PUNTAJE_CON` (difieren en 2017 y 2018), y si 2020/2021 son copias. Hasta entonces se usa `PUNTAJE`, igual que el mapa.
- Para que la versión desplegada muestre la comparación hay que volver a tomar la foto.

## Addendum (2026-11-15): la LOM de cada escuela, con nombres, como el sistema original

**Contexto.** El sistema original tiene «Seleccione la asignatura/cargo para obtener el orden de mérito»: el docente elige una de
las escuelas y materias en que se inscribió y ve una LOM recortada (~12 docentes) con apellido y nombre, puntaje, referencia y
alcance. La Lista de Orden de Mérito es pública: se publica por escuela con nombres. La comparación por departamento de este ADR
quedaba lejos de lo que el docente ya conoce.

**Decisión.**

1. «Cómo te ubicás frente a otros inscriptos» pasa a la sección **«Formulario C - Listado de orden de mérito»** de «Mis puntajes»
   (la otra es «Formulario C - Puntaje de inscripción»). Como el original, se elige escuela y asignatura o cargo con dos filtros
   (solo las del docente en la LOM vigente, clasifique o no) y se ve una lista a la vez; arranca en la primera. Cada sección se
   puede imprimir con el membrete del portal: el puntaje de inscripción por escuela (código, asignatura o cargo, puntaje,
   referencia y alcance) y la lista elegida.
2. **Fuente:** `T_DETALLES_INSC_JD` de la convocatoria vigente, con el criterio de la vista de la Junta `VT_LOMUNICACONV_JD`:
   solo los que clasifican (`ID_REFERENCIA = 0`), los supletorios (alcance 06/07) al final y, en cada grupo, de mayor a menor
   puntaje (empate: apellido y nombre). Usa el índice `IDX_DETINSJD_MATGRA` (escuela, materia, cargo): ~100 ms por lista. En
   2019, `PUNTAJE` de la inscripción coincide con `T_PUNTAJES_JD` (definitivo) en el 99,99 % de las filas y la referencia en todas.
3. **Recorte:** 12 lugares, 6 por encima del docente; si está cerca del final, los últimos 12; si no clasifica, los primeros 12.
   La fila del docente va marcada («Vos»).
4. **Datos de terceros:** viaja apellido y nombre (de `VT_EMPLEADOS`), puntaje, referencia y alcance; nunca CUIL, DNI ni
   `ID_EMPLEADO`. El docente solo puede ver las listas de sus propias escuelas y materias: `GET /api/puntaje/lom` no recibe
   parámetros y arma los pares desde su LOM. En el modo foto no hay listas (la foto no guarda datos personales). Esto cambia lo
   dicho en el punto 7 («nunca se devuelve el puntaje de otra persona») solo para esta lista, que es pública en el original.
5. **El gráfico se mantiene** dentro de cada lista, en un desplegable «Cómo se reparten los puntajes», con los mismos agregados por
   materia y departamento, el mismo corte k ≥ 10 y la marca del puntaje LOM. Se saca el aviso de «datos históricos».
6. Una conexión por pedido y una consulta por lista, con caché de 10 minutos por docente, escuela y materia.

**Pendiente:** confirmar con la cátedra que mostrar la LOM recortada con nombres está bien para la demo (es lo que hace el sistema
original) y con la DBA si la LOM publicada de la Junta usa `PUNTAJE` o `PUNTAJE_CON`.

# ADR-027: Valoración de títulos y antecedentes según el Dto. 257/19

**Estado:** Aceptado   La acumulación, los cursos, la caducidad y la antigüedad que asigna el vocal los ajusta el ADR-039.
**Fecha:** 2026-10-07  
**Reemplaza:** el baremo provisorio (`fixtures/baremo-provisorio.json`) del puntaje estimado  
**Relacionado:** ADR-009 (alcance de título), ADR-010 (motor LOM, con addendum), ADR-022 (asistente), ADR-024 (simulador), ADR-025 (ayudas para decidir)

## Contexto

El portal estimaba el puntaje con un baremo provisorio: título base 25/18/12 según la condición, antigüedad de 1 punto por
año con tope 30 y cursos a 0,017 por hora. Esos valores eran un supuesto a la espera del baremo oficial. El baremo oficial
para el nivel secundario es el **Decreto 257/19** («Para la valoración de títulos y antecedentes para el ejercicio de la
docencia en el nivel secundario en todas sus orientaciones y modalidades»), y lo contradice: el título docente vale 60 y la
antigüedad tiene tope 10, entre otras reglas que no estaban.

Además, el legajo **no registra varios datos que el decreto usa** (el nivel del título, el tipo de formación, si la antigüedad
fue frente a alumnos, la autoría de una publicación…). Los formularios reflejan la estructura de la base de datos y **no se
modifican**.

## Decisión

El puntaje estimado se calcula con un **motor de valoración del Dto. 257/19** que aplica todo el decreto. Sus valores están en
`fixtures/baremo-decreto-257.json` (cada bloque con su artículo) y las reglas en `server/src/modules/puntaje/decreto-257.ts`.

### Cómo calcula

- **Por materia o cargo** (Art. 32): el título vale según la condición con que habilita esa materia, y solo se clasifica en lo que
  habilitan los títulos (Arts. 2 y 32): si ningún título la habilita, esa materia no suma.
- **Capítulo por capítulo, con todos los topes.** Cada punto es una **partida** con su capítulo, su artículo, su concepto y su
  origen (`dato` de la base o `deducido` del texto). Un tope aplicado es una partida negativa, para que se vea por qué no suma más.
- Los Caps. IV a VI no dependen de la materia: se calculan una vez por legajo y valen igual para todas.

| Cap. | Artículos | Regla aplicada |
|---|---|---|
| I | 1 | Docente 60 · habilitante 40 (superior) o 20 (secundario) · supletorio 25 (superior) o 10 (secundario); vale el título que más da para la materia |
| II | 3 y 4 | Sobre cualquier clasificación que sume: +20 y +15 por hasta dos títulos más de nivel superior; tope 35; maestro o profesor de primaria/inicial no acumulan a un docente |
| III | 5 a 7 | Año o fracción ≥ 6 meses; 1 por año si el grupo coincide con lo que aspira (frente a alumnos u otros cargos), 0,60 si no; tope 10; servicios simultáneos no se suman (por fechas); inicial/primario solo para el mismo cargo; jerárquicos no directivos 1 por año, tope 6 |
| IV | 8 a 13 | Posgrados 4/6/8 · postítulos 4/6/8 (200/400/600 h) · cursos aprobados 0,017/h y dictados 0,020/h, de 20 a 150 h, sin réplicas · tope 70 entre los Arts. 8 a 11 · idiomas 1,5 · sin Red 0,010/h, tope 6 |
| V | 14 a 19 | Arts. 14, 15, 16 y 18 **no se valoran** (convocatoria según el decreto) · salidas educativas 1/0,5, tope 8 · docente orientador 1/0,5, tope 5 |
| VI | 20 a 28 | Libros 4/2 (tope 8) · artículos de único autor 0,25 (tope 2) · comisiones, becas y adscripciones (tope 2 c/u) · concepto Sobresaliente 2 / Distinguido 1, el mejor de cada año, últimos 3 años, tope 6 · promedio del título 4/3/2 · estudios parciales 1 por año, una carrera, tope 5, dejan de contar con el título · vocal de junta, tope 8 |
| VII | 31 | Los antecedentes cuentan desde el título o el ingreso a la docencia (si las fechas están cargadas) |

### Qué se deduce y cómo (los formularios no cambian)

Cada valor deducido se marca como tal en el desglose, con la regla que lo produjo. Ante la duda se toma el valor menor del artículo.

| Dato que pide el decreto | De dónde sale |
|---|---|
| Nivel del título (Art. 1) | Del nombre: «Técnico…», «Bachiller…», «Perito…» sin «superior» = secundario; el resto = superior |
| Tipo de formación (Cap. IV) | Del concepto y la descripción: «maestría», «doctorado», «especialización», «postítulo», «diplomatura superior», «actualización académica», «dictado», «idioma»…; el emisor (Red Provincial / ISEP u otros) separa los Arts. 10 y 13 |
| Horas | Campo de horas o, si no está, el número de horas que diga el texto |
| Grupo de la antigüedad (Arts. 5 y 6) | Del cargo: preceptor, secretario, bibliotecario, coordinador… = otros cargos; el resto = frente a alumnos. Zona desfavorable: no se registra, no se aplica |
| Años de antigüedad | Años/meses/días o fechas; si no hay, los años que diga la descripción |
| Trabajo institucional, publicaciones y otros (Caps. V y VI) | De la descripción: artículo por palabras clave, rol (organizador/acompañante), autoría (única/coautoría) y duración; sin rol o duración, el menor |
| Calificación y año del concepto (Art. 24) | Del texto del concepto y su año |

Lo que no se puede leer queda **«sin estimar»** con el motivo, y lo que el decreto no valora (Arts. 14, 15, 16 y 18, réplicas,
cursos de menos de 20 h, concursos…) queda en **«no suma»** con el motivo.

### Interpretaciones adoptadas

- Los cursos de los Arts. 10 y 11 de más de 150 h cuentan hasta 150 h.
- **No se aplica la caducidad** de los Arts. 25 y 27.
- La acumulación (Cap. II) no exige que el título acumulado habilite la materia.
- Con la valoración LOM oficial (sin desglose), las ayudas no estiman títulos y solo suman antigüedad hasta el tope sobre la que
  informa la LOM.
- La antigüedad docente que carga el vocal de la constancia de servicios **reemplaza** a la que declaró el aspirante para los
  Arts. 5 y 6, porque es el dato oficial y sumar las dos la contaría dos veces. La declarada queda en el legajo, en «no suma», con el
  motivo. No la reemplaza la antigüedad directiva, que el estimado no valora, y los cargos jerárquicos del Art. 7 siguen contando.

### Dónde se usa

`PuntajeService` (`componentesDe`, `estimarItem`, `estimarItems`) es la única entrada: lo usan **Mi puntaje** (con la tabla por
capítulo y el desglose «Cómo se calcula tu puntaje»), el **simulador** (antigüedad hasta 10; un título nuevo se valora con el
motor, con nivel y acumulación), las **ayudas para decidir** (cuánto suman los pendientes, con el artículo de cada uno), la
**propuesta del Formulario C** y el **asistente**. El validador agrega `competenciasPorTitulo`, que devuelve la condición de cada
título para cada materia en una sola pasada por el alcance.

## Alternativas consideradas

- **Pedir los datos que faltan en el legajo:** descartada; los formularios responden a la estructura de la base y no se cambian.
- **Dejar «sin estimar» todo lo que no tenga el dato exacto:** descartada; el puntaje quedaría muy por debajo del real. Se
  prefirió deducir y mostrar qué se dedujo.
- **Seguir con el baremo provisorio hasta tener el motor ministerial:** descartada; el decreto es la norma vigente y el baremo
  provisorio daba números que no coinciden con ella.

## Consecuencias

**Positivas**

- El estimado sigue la norma vigente y se explica artículo por artículo.
- El docente ve qué no suma y por qué, y qué le falta cargar.

**Negativas / limitaciones**

- Las deducciones pueden equivocarse (por ejemplo, el nivel de un título técnico superior sin la palabra «superior»): por eso
  se marcan.
- No se verifican requisitos que la base no registra: afinidad con la materia, gestión oficial del concepto, títulos intermedios
  o por equivalencia, zona desfavorable, participaciones por ciclo lectivo.
- Sigue siendo una estimación: la valoración la hace la junta.

## Addendum — acumulación sobre el nivel medio (2026-11-05)

El encabezado de los Arts. 3 y 4 nombra los incisos a, b y c, pero el texto agrega que los títulos de nivel secundario solo
acumulan títulos de nivel superior, y la Junta lo hizo: 742 filas de `T_DETALLES_INSC_JD` con caso 03 o 07 tienen acumulación, y
su `PUNTAJE` suma las dos partes (10 + 20 = 30 como mínimo). Se sigue a la Junta: acumula sobre cualquier clasificación que sume,
siempre con títulos de nivel superior.

## Addendum — participaciones por ciclo lectivo (2026-11-05)

Los Arts. 17 y 19 toman hasta dos participaciones por ciclo: valen las que más suman y las demás quedan en «no suma». El año
sale de la descripción; sin año no se puede saber y se cuentan.

## Addendum — título valorado por el vocal (2026-11-05)

El caso que elige el vocal para una materia reemplaza a la sugerencia en el Cap. I (ADR-033). El
nivel sale del alcance cuando lo trae; deducirlo del nombre del título queda para cuando la fuente no lo informa.

## Addendum — Mi puntaje muestra solo el estimado (2026-11-07)

La pantalla es la del puntaje estimado, no la de la LOM: sin inscripción pide inscribirse aunque el lab tenga una valoración, y ya no lista la tabla oficial. El servidor sigue informando `oficial` y `origen`, que usan el mapa y el perfil.

El docente tampoco ve la competencia del título ni qué dato se dedujo. Se aclara una sola vez que el número es una estimación y, en cada materia, si ya la valoró la Junta. Las marcas `origen` y `nota` de cada partida siguen en la API para explicar el cálculo. El título se nombra como «Título: …», sin condición ni nivel.

## Addendum — un solo puntaje estimado (2026-11-07)

El mismo docente veía dos puntajes: Mi puntaje, Perfil y Mapa tomaban el más alto de las materias de su inscripción; Asistente y Simulador, el más alto de todas las materias que sus títulos habilitan. Si el título vale más en una materia en la que no se inscribió, el segundo daba más. Ahora todos usan las materias de la inscripción, porque es donde compite. Sin inscripción se usan las que puede dar, para orientarlo antes de inscribirse. Las simulaciones de un título nuevo siguen comparando sobre las materias que ese título habilita.

## Referencias

- Dto. 257/19, insumo para docentes del 21-11-19.
- Código: `src/server/src/modules/puntaje/decreto-257.ts`, `decreto-257.deducciones.ts`, `puntaje.service.ts`,
  `src/fixtures/baremo-decreto-257.json`; tests `decreto-257.test.ts` (un caso por regla).
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml), `GET /api/puntaje/inscripcion` (`desglose`, `partidas`).

## Addendum (05/11/2026): antigüedad declarada como en la web de la Junta

- El formulario de antigüedad del legajo copia el de la web de la Junta (`T_EMP_ANTIGUEDADES`): se puede declarar antigüedad en escuelas de Córdoba (antes el portal la rechazaba y aceptaba solo la de otra provincia o la universitaria).
  - Fecha desde y fecha de emisión del certificado son obligatorias. Número de resolución, establecimiento y totales son opcionales.
  - El cargo se busca en los cargos activos de `T_GRADOS_JUNTA`, o se escribe si no está.
  - Si no es universitario, el establecimiento se busca en el padrón, o se escribe.
- No cambia la valoración: la antigüedad que carga el vocal desde la constancia sigue reemplazando a la declarada para los Arts. 5 y 6, así que declarar la de Córdoba no la cuenta dos veces en el estimado.

## Addendum — título principal sin alcance y contraste del puntaje (2026-11-06)

Si la materia no tiene alcance para los títulos del docente y el vocal pone un caso, el título principal es el del nivel del caso (el superior para Docente). Antes era el primero de la lista: suele ser el secundario, y el superior se contaba como acumulación.

Contraste con 182 docentes de la convocatoria 2019, con el cálculo real del portal, sus antecedentes de Oracle y el caso de la Junta: la diferencia viene casi toda de la acumulación. La Junta acumula mucho menos de lo que permite la lectura del decreto de la línea anterior, y el nivel real de `T_TITULOS` no alcanza para explicarlo. La regla queda para confirmar con la Junta: hasta entonces, el estimado puede dar más que el puntaje oficial.

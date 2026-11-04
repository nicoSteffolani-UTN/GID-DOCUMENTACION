# ADR-027: Valoración de títulos y antecedentes según el Dto. 257/19

**Estado:** Aceptado  
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
| II | 3 y 4 | Sobre una clasificación docente o de nivel superior: +20 y +15 por hasta dos títulos más de nivel superior; tope 35; maestro o profesor de primaria/inicial no acumulan a un docente |
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

## Referencias

- Dto. 257/19, insumo para docentes del 21-11-19.
- Código: `src/server/src/modules/puntaje/decreto-257.ts`, `decreto-257.deducciones.ts`, `puntaje.service.ts`,
  `src/fixtures/baremo-decreto-257.json`; tests `decreto-257.test.ts` (un caso por regla).
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml), `GET /api/puntaje/inscripcion` (`desglose`, `partidas`).

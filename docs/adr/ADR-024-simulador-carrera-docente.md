# ADR-024: Simulador de carrera docente

**Estado:** Aceptado  
**Fecha:** 2026-10-05  
**Reemplaza (en la pantalla):** ADR-011 (simulador de puntaje LOM, mock US-11)  
**Relacionado:** ADR-009 (alcance de título), ADR-010 (motor LOM), ADR-012 (capacitaciones), ADR-019 (semáforo LOM del mapa), ADR-020 (vacantes), ADR-022 (asistente)

## Contexto

La pantalla `/simulador` mostraba tres escenarios fijos («sin cursos», «un curso», «dos cursos») con el puntaje que cada uno daría. Le servía poco al docente: no podía elegir qué simular y no veía qué cambiaba en su carrera, que es lo que le interesa: **a qué escuelas llega**.

## Decisión

El simulador pasa a ser una sesión de juego: el docente parte de su puntaje, arma un escenario y ve el efecto sobre el mapa. **No se guarda nada** (ni escenarios ni resultados) y el cálculo corre en el navegador, así que cada cambio se refleja al instante. Desde la ampliación del 2026-10-12, «Mi plan» queda en la pestaña del navegador mientras esté abierta, nunca en el servidor.

### Qué puede mover el docente

- **Cursos** del catálogo que todavía no hizo, con los puntos del Dto. 257/19 Art. 10 (0,017 por hora, cursos de 20 h o más).
- **Años de antigüedad** (0 a 5): 1 punto por año según el Dto. 257/19 (ADR-027). Con puntaje estimado se respeta el tope de 10 menos lo que ya tiene; con valoración LOM oficial no se aplica tope, porque no se sabe cuánto de la antigüedad ya está contado en esa valoración.
- **Un título nuevo** del catálogo de alcance. Desde ADR-027 suma la diferencia entre el mejor estimado con y sin ese título según el Dto. 257/19 (condición y nivel del título y acumulación de títulos), y muestra qué materias con vacantes abiertas habilita.

### Qué ve

- Puntaje de hoy y simulado, escuelas «a su alcance» (semáforo verde) hoy y con el escenario, y la distribución de semáforos. El semáforo usa las mismas reglas que el mapa: verde si el promedio de la escuela no supera el puntaje, amarillo hasta 10 puntos por encima, rojo si lo supera por más, azul sin promedio.
- **Escuelas que se le abren**, agrupadas por localidad, con las vacantes abiertas en materias que puede tomar (sus títulos actuales más el simulado).
- **Qué curso rinde más:** los cursos ordenados por cuántas escuelas nuevas abren sumados al escenario actual, no por puntos. Solo se listan los que abren alguna.
- **Las que siguen:** las escuelas más cercanas a su alcance, con los puntos que faltan.
- **Mi meta:** el docente elige una escuela y se arma el camino más corto para 0 a 5 años de espera. Para cada cantidad de años, descuenta los puntos de antigüedad y elige los cursos que cubren el resto (el mismo plan de cursos del asistente, ADR-022). Un botón aplica ese camino al escenario.

### Datos y API

- `GET /api/simulador/carrera`: punto de partida (puntaje y su origen, antigüedad y tope, escuelas livianas con promedio y vacantes abiertas, cursos pendientes, títulos que no tiene, materias con vacantes que ya habilita).
- `GET /api/simulador/carrera/titulo?codigo=`: efecto de un título nuevo (materias con vacantes que habilita, mejor condición y puntos de título).
- `GET /api/simulador/carrera/meta?escuelaId=`: caminos hacia el promedio de una escuela.
- Todos de solo lectura, con rol aspirante, sobre los mismos datos que el mapa, las notificaciones, las capacitaciones y el validador. El cálculo de cada escenario vive en `simulador-calculo.ts`, sin dependencias, con tests propios.
- `GET /api/simulador/ascenso` (ADR-011) se mantiene porque el asistente lo usa para la intención «simular mi trayectoria»; la pantalla ya no lo consume.

### Conexión con el asistente

El asistente abre el simulador con la escuela ya elegida como meta (`/simulador?escuelaId=`), y la pantalla acepta también `cursos=` con identificadores de curso para entrar con un escenario cargado.

## Ampliación (2026-10-05): meta por zona y materia, comparación y margen de seguridad

- **Meta por zona o materia.** Además de una escuela, el docente puede elegir una localidad (o departamento) o una materia
  que puede dar. Para esos casos hay hasta tres niveles —la próxima escuela, la mitad y todas— sin repetir escuelas, cada uno
  con sus caminos de 0 a 5 años. La materia se resuelve contra el catálogo de materias de cada escuela del padrón (no solo las
  que tienen vacantes hoy, que en el lab son muy pocas). La pantalla acepta `zona=` y `materia=` en la URL, como `escuelaId=`.
- **Comparación de escenarios.** Dos escenarios, A y B, en la misma pantalla y sin guardar nada: el B nace como copia del A para
  cambiar una sola cosa. La tabla compara puntaje, escuelas a su alcance, seguras con margen, cursos y horas, años y título.
  Gana el que llega a más escuelas; a igualdad, el que tiene más seguras y, después, el de menos horas de cursada.
- **Margen de seguridad.** Los promedios son históricos, así que no hay un dato de variación para armar una banda estadística y
  no se inventó uno: el docente elige un colchón de 0 a 10 puntos (3 por defecto). Con él, una escuela es **segura** si su promedio
  más el margen no supera el puntaje, **justa** si llega hoy pero un aumento del promedio lo dejaría afuera, **cerca** si le faltan
  hasta 10 puntos y **lejos** más allá. Con margen 0 coincide con el semáforo del mapa. La meta también se calcula con el margen
  (`margen=` en la API) y se recalcula al moverlo.

## Ampliación (2026-10-05): vacantes, seguimiento de escuelas y mapa

- **Vacantes que ya puede tomar.** Una tarjeta lista las escuelas a las que el docente llega hoy y que tienen vacantes abiertas en
  materias que habilitan sus títulos. En «Escuelas que se te abren» hay un filtro «solo con vacantes abiertas para mí» y un resumen
  de cuántas tienen. El lab tiene muy pocas publicaciones abiertas, así que casi siempre el resultado es chico; la pantalla lo dice.
- **Seguir una escuela.** Cada escuela que se abre tiene un botón «Avisarme cuando haya vacantes». Es el mismo interés del mapa
  (`POST/DELETE /api/mapa/interes`) y por lo tanto activa los avisos de vacantes del portal (ADR-020). Es la única acción del simulador
  que guarda algo, y solo ocurre cuando el docente toca el botón; `GET /api/simulador/carrera` informa cuáles sigue (`intereses`).
- **Demanda histórica: no incluida.** Mostrar en qué escuelas y materias suele haber cobertura exigiría consultar el histórico de
  publicaciones de la Junta (`T_PUBLICACIONES`, 2011–2019), que hoy el portal no lee. Se descartó para no agregar una integración
  nueva con Oracle ni inventar un indicador.
- **Mapa dentro de la pantalla.** Leaflet, cargado solo en el navegador, dibuja cada escuela con el nivel que alcanza con el escenario
  y resalta con un borde azul grueso las que se abren respecto de hoy. Se redibuja al cambiar el escenario, el margen o los datos. Las
  escuelas sin promedio no se dibujan.

## Ampliación (2026-10-05): línea de tiempo y «qué me conviene hacer ahora»

- **Tu carrera año a año.** Un gráfico de líneas compara, de hoy a 5 años, las escuelas a su alcance en dos casos: solo esperando
  (la antigüedad suma sola) y haciendo hoy los cursos y el título del escenario y esperando. La diferencia entre las curvas es lo que
  aporta lo que el docente decide hacer; los años propios del escenario se reemplazan por los de cada punto para no contarlos dos veces.
  Si el escenario no tiene cursos ni título, las curvas coinciden y se dibuja una sola.
- **Gráfico.** SVG propio, sin librerías, con las reglas de la guía de visualización del proyecto: líneas de 2 px, marcadores de más de
  8 px con anillo, leyenda, etiquetas solo en el extremo y solo si no se pisan, cruz y tooltip con puntero o flechas del teclado, y una
  tabla «ver los números año por año». Usa el azul y el naranja del portal (validados: separación para daltonismo ΔE 24,6 y visión normal
  34,5); como el naranja queda bajo 3:1 de contraste sobre el fondo, las curvas se distinguen además por la forma del marcador (círculo y
  rombo), las etiquetas y la tabla.
- **Asistente: `proximo_paso`.** «¿Qué me conviene hacer ahora?», «¿por dónde empiezo?» o «¿qué curso me abre más escuelas?» recomiendan hasta
  tres cursos pendientes ordenados por las escuelas nuevas que abren sumados al puntaje actual (a igualdad, el más corto) y llevan al
  simulador con el curso ya puesto (`/simulador?cursos=`). Si ningún curso abre una escuela por sí solo, informan la escuela más cercana
  a su alcance y lo que falta para llegar. Lo calcula `CarreraService.getProximoPaso`; el modelo de lenguaje solo clasifica el pedido
  (ADR-023), no recomienda.

## Ampliación (2026-10-05): exportar el plan

- **Llevate tu plan.** Un resumen de lo armado en la pantalla —escenario, resultado, año a año, escuelas que se abren (hasta 15, el resto
  contado) y meta con sus caminos— que el docente puede **imprimir o guardar como PDF** (cuadro de impresión del navegador) o **copiar
  como texto** plano para pegarlo en un mail o un mensaje. Con el escenario vacío y sin meta, los botones quedan deshabilitados con una
  explicación. Si se está comparando, se exporta el escenario que se edita y el documento lo indica (A o B).
- **Sin guardar nada.** El plan se arma en el navegador (`armarPlan`, función pura) y el documento impreso reutiliza el mecanismo del
  Formulario C: en pantalla no se ve y al imprimir es lo único que se muestra, con el membrete del portal, la fecha y el nombre del docente
  (no el CUIL). Termina siempre con la advertencia de que es una estimación y no el orden oficial de la Junta.
- **Copiar sin permiso.** Si el navegador no deja usar el portapapeles (por ejemplo, página sin HTTPS), se copia con un campo temporal; si
  tampoco es posible, se avisa y se ofrece el PDF.

## Ampliación (2026-10-07): pantalla pensada para el docente

Con todas las funciones juntas la pantalla tenía 15 secciones y 40 controles en una sola columna (casi 5 pantallas en
escritorio y 8 en celular): lo principal, armar el escenario, recién aparecía en la tercera pantalla, el resultado quedaba
lejos de los controles y había mucha jerga. Se reorganizó sin quitar ninguna función:

- **Situación arriba, siempre visible:** puntaje, a cuántas escuelas llega hoy y dos botones para los ajustes que valen para todo
  el simulador: **dónde** (zona y radio) y **colchón** (el margen de seguridad, con ese nombre). Cada uno se abre debajo y se cierra
  con «Listo».
- **Tres pestañas, una por pregunta del docente:** «Hoy» (cómo está, vacantes que ya puede tomar, mapa y las escuelas más cercanas,
  con «¿Cómo llego?»), «¿Qué pasa si…?» (esperar, hacer cursos, sacar otro título) y «Llegar a una escuela» (la meta). Se manejan
  con el teclado según el patrón de pestañas de WAI-ARIA y el asistente abre la que corresponde (`escuelaId`, `zona` o `materia`
  abren la meta; `cursos` abre «¿Qué pasa si…?»).
- **El resultado al lado de los controles:** en escritorio queda fijo a la derecha; en celular, un resumen corto queda pegado arriba
  y lleva al resultado completo. Desde ahí se ve el mapa, se imprime o copia el plan y se empieza de cero.
- **Menos pasos para elegir:** los años son botones (Hoy, 1 a 5) en lugar de un control deslizante; arriba de los cursos van los tres
  que más escuelas le abren, con «Sumar»; la lista completa queda plegada, con buscador. Los nombres de cursos, que el catálogo trae
  en mayúsculas, se muestran en tipo oración (`nombreLegible`).
- **La meta se lee como caminos:** «Empezando hoy: 6 cursos (225 h)», «Esperando 3 años: 2 cursos (50 h)», cada uno con «Probar
  este camino», que lo carga en «¿Qué pasa si…?» y lo avisa. Sin meta elegida, sugiere las escuelas más cercanas.
- **Textos llanos:** «colchón» en lugar de margen, «Oficial, del listado de orden de mérito» en lugar de «Valoración LOM», «ideas»
  en lugar de escenarios A y B, y una explicación plegable de qué es el promedio de una escuela.

## Ampliación (2026-10-12): simulador guiado por preguntas

Aun con las pestañas, para usar el simulador el docente tenía que entender primero sus herramientas (escenarios, colchón,
promedio, caminos) antes de obtener una respuesta, y lo más probable era que no lo usara. Se reemplazan las pestañas de la
ampliación anterior por **preguntas que se responden al primer toque**, sin quitar ninguna función:

- **«¿Qué querés saber?»** es la entrada: tres tarjetas grandes con una pista armada con sus datos («Por ejemplo, esperando
  3 años entrás a 6 escuelas más», «a IPEM 522 te faltan 0,8 puntos»). Cada pregunta es una vista con su dirección
  (`?ver=escuelas`, `conviene`, `llegar`, `plan`, `combinar`), así que el botón «Atrás» del navegador vuelve a la anterior; al
  cambiar de vista el foco va al título de la respuesta y la pantalla solo se desplaza si la respuesta quedó fuera de la vista.
- **¿A qué escuelas puedo entrar?** Una frase («Con tus 78,5 puntos hoy entrás a 15 de las 40 escuelas») y tres niveles en
  lugar de cinco: **entrás**, **te falta poco** (hasta 10 puntos) y **te falta mucho**. Si entra por menos de 3 puntos (el colchón
  por defecto) la escuela dice «Por poco». Debajo: vacantes que ya puede tomar, las escuelas a las que entra por localidad (con
  la campana para seguirlas), las más cercanas con «¿Cómo llego?» y el mapa con una leyenda en las mismas palabras.
- **¿Qué me conviene hacer para sumar puntos?** Hasta tres recomendaciones (`recomendaciones`), cada una contada por separado
  desde el puntaje de hoy: **esperar** la cantidad de años que más escuelas abre por año de espera, sin pasar los que todavía
  suman por el tope de antigüedad; **el curso que más escuelas abre**; y **sacar otro título**, que se elige en la misma tarjeta
  y dice cuánto suma, qué materias con vacantes habilita y cuántas escuelas abre. Van de la que más escuelas abre a la que menos;
  el título va antes de lo que no abre ninguna porque es lo que más puede sumar (10 a 60 puntos según el Art. 1). Cada una tiene
  «Agregar a mi plan».
- **¿Cómo entro a la escuela que quiero?** Para elegir: **tus escuelas** (las de su Formulario C y las que sigue en el mapa; para
  eso `GET /api/simulador/carrera` informa `inscriptas`), las más cercanas y un buscador; la meta por localidad o materia queda
  detrás de un enlace. La respuesta se mide contra el promedio, **sin colchón** (`margen=0`), porque «cuánto me falta» es lo que
  entiende el docente: «Te faltan 0,8 puntos». Después, hasta tres formas de llegar (`opcionesDeCamino`): **este año, con
  cursos**; **esperando menos y cursando menos**; y **sin hacer cursos**, solo con la antigüedad. Si ninguna llega en 5 años, lo
  dice y sugiere un título. «Elegir este camino» pone en el plan sus años y cursos (el título queda) y avisa si reemplazó algo;
  todas las combinaciones de años y cursos siguen a mano, plegadas.
- **Mi plan** es el escenario A: lo que fue agregando desde las respuestas. Muestra el resultado en una frase y en cifras, cada
  cosa con «Quitar», la meta medida con el plan («con este plan llegás» o lo que faltaría), las escuelas nuevas, imprimir o
  guardar como PDF, copiar, «Empezar de nuevo» y «Ajustar en detalle». La línea de tiempo queda plegada. En las demás preguntas
  lo acompaña una barra «Tu plan: …» que lleva a esta vista.
- **El plan queda en la pestaña.** Para que no se pierda al recargar o al ir al mapa y volver, el plan (años, cursos y el código del
  título), la meta, la zona y el colchón se guardan en el `sessionStorage` del navegador, como la conversación del asistente: por
  docente (clave `gid_simulador_` más el CUIL), solo en esa pestaña y hasta que se cierra; se borran al cerrar la sesión y nunca
  llegan al servidor. Al volver se revisa lo guardado (`simulador-sesion.ts`): se descartan cursos que ya no están en el catálogo
  y el título se vuelve a calcular por si cambió el legajo. Si el asistente trae otra meta, reemplaza a la guardada.
- **Armar mi propia combinación** reúne las herramientas para quien las quiera: la pestaña «¿Qué pasa si…?» de antes, con la
  comparación de dos ideas (la A es «Mi plan»), el mapa, la línea de tiempo y el **colchón**, que solo aparece acá.
- **Asistente.** `escuelaId`, `zona` y `materia` abren «¿Cómo entro…?» con la meta y `cursos` abre «Mi plan» con esos cursos
  sumados. La dirección se reemplaza para que «Atrás» no repita el pedido, y funciona también si el docente ya estaba en la pantalla.
- **Ediciones de un mismo curso.** El catálogo repite algunos cursos (mismo nombre en varias ofertas, a veces con la misma
  resolución) y el camino a una escuela llegaba a proponer el mismo curso tres veces. Como `cursosPendientes` ya trataba igual
  nombre como el mismo curso, el plan de cursos (`planDeCursos`, compartido con el asistente) y las recomendaciones de cursos ahora
  cuentan cada curso una sola vez, la oferta que más suma. Con el catálogo de ejemplo esto cambia algunos resultados: por
  ejemplo, un docente que con ediciones repetidas «llegaba» a una escuela esperando 2 años y haciendo 13 cursos ahora recibe la
  respuesta de que con años y cursos no le alcanza.

## Alternativas consideradas

- **Recorrido paso a paso (asistente con «Siguiente»):** se descartó para la pantalla: guía más, pero para cambiar una cosa y ver el
  efecto hay que volver atrás. Las preguntas guiadas no son un recorrido: cada una responde al primer toque, sin completar nada antes.
- **Pestañas «Hoy», «¿Qué pasa si…?» y «Llegar a una escuela» (2026-10-07):** reemplazadas por las preguntas guiadas (2026-10-12),
  porque pedían entender las herramientas antes de obtener una respuesta. La pestaña «¿Qué pasa si…?» sigue como «Armar mi propia
  combinación».
- **Meta guiada con colchón:** se descartó; el colchón queda en la combinación propia y la pregunta «¿Cómo entro…?» responde cuánto
  falta para el promedio, que es lo que el docente pregunta.

- **Calcular cada escenario en el servidor:** se descartó; con ~800 escuelas el cálculo es una suma y una comparación, y pedirlo en cada movimiento del control haría la pantalla lenta sin ninguna ventaja.
- **Generar el PDF en el servidor:** se descartó; el cuadro de impresión del navegador ya guarda como PDF, no suma dependencias y no manda datos al servidor.
- **Guardar escenarios por usuario:** queda para una versión posterior. Se eligió una sesión de juego para no sumar persistencia ni procesamiento; la comparación A/B vive solo en la pantalla. Desde el 2026-10-12 «Mi plan» se conserva en la pestaña del navegador (sin servidor), para no perderlo al navegar.
- **Mapa embebido en la pantalla:** incorporado en la ampliación; cada escuela sigue enlazando al mapa completo del portal.
- **Banda de incertidumbre calculada:** se descartó porque no hay datos de variación de los promedios; se prefirió un margen elegido por el docente.

## Consecuencias

**Positivas**

- Muestra el efecto en escuelas y vacantes y no solo en puntos, y permite trazar un camino concreto a una escuela.
- Sin persistencia ni procesamiento pesado en el servidor.

**Negativas / limitaciones**

- El promedio de cada escuela es histórico y general: el sistema no tiene promedios por materia. La meta por materia usa el promedio general de las escuelas que la dictan.
- El catálogo de cursos es de ejemplo (ADR-012) y los puntos son estimaciones, no el orden oficial de la junta.
- Con valoración LOM oficial no se conoce el desglose del puntaje, por eso no se aplica el tope de antigüedad.
- Las vacantes son las publicaciones abiertas del año en curso; si no hay publicadas, las escuelas figuran sin vacantes aunque estén a su alcance.

## Referencias

- Código: `src/server/src/modules/simulador/` (`carrera.service.ts`), `src/web/src/app/features/simulador/`, `src/web/src/app/core/simulador/`
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml), tag Autogestión

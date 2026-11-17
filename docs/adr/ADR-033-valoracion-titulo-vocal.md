# ADR-033: Valoración del título por el vocal

**Estado:** Aceptado  
**Fecha:** 2026-11-05  
**Relacionado:** ADR-009 (alcance título), ADR-027 (Dto. 257/19), ADR-028 (persistencia), ADR-029 (tablas de la Junta)

## Contexto

El portal deducía la condición del título con el alcance, pero nadie podía corregirla: el vocal solo veía una etiqueta
por materia. En la Junta esa decisión es del vocal y se guarda por fila de `T_DETALLES_INSC_JD`, en
`ID_TITULO_HABILITACION`, con el catálogo `T_TITULOS_HABILITACION_JD`.

Ese catálogo tiene dieciocho filas. Las de un dígito son las anteriores al Dto. 257/19, con otros puntajes. Las seis de
dos dígitos son las vigentes y sus puntos coinciden con el Art. 1. Además, el dump `TITULOS_ALCANCES` trae el nivel en la
columna `ALCANCE` (`HABILITANTE DE NIVEL SUPERIOR`), que el portal descartaba para deducirlo del nombre del título.

## Decisión

1. **Dónde.** La revisión del legajo suma un cuarto botón, «Valoración», junto a Pendientes, Rechazados y Aceptados. Muestra
   los títulos aceptados y las materias inscriptas con su sugerencia; lo demás que habilita cada título se abre en una
   ventana aparte, para no mover la pantalla. Reemplaza al
   bloque «Inscripción», que empujaba la lista de antecedentes hacia abajo.
2. **Sobre qué.** El vocal decide solo sobre las materias y cargos del Formulario C, una vez por materia aunque esté en
   varias escuelas. Cada título habilita 27 materias en promedio y hasta 139: lo que el docente no eligió se muestra como
   dato. La Junta tampoco tiene dónde guardar esas decisiones.
3. **Casos.** Los seis vigentes, con los códigos de la Junta: `01` Docente (60), `02` Habilitante de nivel superior (40),
   `03` Habilitante de nivel medio (20), `06` Supletorio de nivel superior (25), `07` Supletorio de nivel medio (10) y
   `05` No reglamentario (0). Los puntos salen del baremo.
4. **Sugerencia.** El título que más vale para la materia, con la misma función que usa el puntaje (`mejorTitulo`), para
   que nunca digan cosas distintas. Si ningún título aceptado la habilita, No reglamentario. Solo cuentan los títulos
   aceptados: no se suman puntos por algo que no está aprobado. Sin ninguno no se muestran materias para valorar y la API
   rechaza el caso.
5. **Efecto.** Lo que elige el vocal reemplaza a la sugerencia en el puntaje, igual que la antigüedad de la constancia
   (ADR-027), y el docente lo ve como «Valorado por la Junta». Mientras no elija, vale la sugerencia. Con No reglamentario la
   materia no suma (Arts. 2 y 32). Si se rechaza el único título aceptado, lo decidido deja de sumar y vuelve a valer al
   aceptar otro. También se aplica en el simulador y en el asistente.
6. **Nivel.** El alcance guarda el nivel que trae el dump y le gana al que se deduce del nombre; la partida deja de figurar
   como deducida. Sin nivel en la fuente (`VT_MAT_GRADO_X_TITULO`), se sigue deduciendo y avisando.
7. **Guardado.** El selector de cada materia viene con la sugerencia: el vocal la confirma, elige otro caso o confirma
   todas las pendientes de una vez (`POST .../valoracion-titulo/confirmar`). Se guarda al instante, en la sección `valoracionesTituloByCuil` del dev-store y en la tabla
   `valoracion_titulo` (migración 009). Va aparte del Formulario C: si el docente lo vuelve a guardar, la decisión sigue.

## Consecuencias

- Contraste con la Junta (2026-11-05, solo lectura): docentes con un solo título, 142.108 filas de `T_DETALLES_INSC_JD` con
  casos vigentes, el 96% de 2019. La sugerencia coincide en el 67,9% (96.491); cuando sugiere un caso con puntos, en el 86,9%
  (47.063 de 54.138). Si difiere, casi siempre sugiere de menos: 42.840 filas contra 2.777 de más. El grupo mayor son títulos
  que no están en el alcance, como los bachilleres que la Junta clasifica supletorio de nivel medio en cargos como preceptor.
  Por eso el vocal puede elegir un caso aunque ningún título aceptado habilite la materia. El mayor desacuerdo con caso es
  Supletorio superior sugerido contra Habilitante superior asignado (2.544 filas); el alcance trae reglamentaciones posteriores
  a 2019, lo que podría explicarlo, sin confirmar.
- Con el dump real, 4.470 de las 74.765 filas tienen un nivel distinto del que se deducía del nombre, así que esos estimados
  se corrigen sin que nadie decida nada: por ejemplo, un habilitante de 40 puede pasar a 20, o un supletorio de 10 a 25.
- Si un título aceptado no habilita la materia y el vocal elige un caso igual, ese caso suma: es su decisión sobre un título
  aprobado. Por eso, si hay otro título aceptado, rechazar el que dio la sugerencia no borra lo decidido.
- El Formulario C sigue mostrando la competencia del alcance: la decisión del vocal se ve en Mi puntaje.
- **Oracle:** la decisión va a `ID_TITULO_HABILITACION` y `VALORACION_TITULO` de las filas del portal (ADR-029); la sugerencia no se
  escribe.
- Si después de valorar se corrige o se rechaza un título, la materia queda para revisar: suma en el botón Valoración y el vocal la
  mantiene, la cambia o la deshace. Lo decidido no se borra solo.
- La valoración no bloquea el cierre de la revisión ni cuenta en «Revisados».
- Las decisiones de materias que el docente quitó del Formulario C quedan guardadas y no se muestran.

## Referencias

- Código: `src/server/src/modules/puntaje/casos-titulo.ts`, `decreto-257.ts` (`mejorTitulo`, `valorarItem`),
  `src/server/src/modules/junta/junta.service.ts`, `src/web/src/app/features/junta/junta-valoracion.ts`.
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml), `PUT /api/junta/aspirantes/{cuil}/valoracion-titulo`.
- Casos de prueba: CP-P2-42, CP-P2-43 y CP-WEB-17 en [`docs/casos-prueba.md`](../casos-prueba.md).

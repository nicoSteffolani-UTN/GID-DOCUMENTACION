# ADR-039: Ajustes del puntaje tras la reunión con el vocal

**Estado:** Aceptado
**Fecha:** 2026-11-14
**Relacionado:** ADR-027 (valoración con el Dto. 257/19), reunión con el vocal del 2026-11-14

## Contexto

El ADR-027 fijó cómo valora el portal con el Decreto 257/19, con algunas interpretaciones propias: la segunda acumulación valía 15, los cursos se contaban hasta 150 h y no se aplicaba la caducidad. En la reunión, el vocal respondió cómo lo hace la Junta, y para la caducidad el grupo decidió guiarse por el decreto.

## Decisión

1. **Acumulación de títulos (Arts. 3 y 4):** la primera vale 20 y la segunda 10, con un tope de 30. Solo acumulan los títulos de más de 1600 h. El portal no tiene la carga horaria de los títulos, así que la marca el vocal en la revisión del legajo («¿Acumula?», Sí o No). Si no está marcada, el estimado cuenta el título y avisa que falta confirmarlo. Si el vocal marca que no, el título no acumula.
2. **Cursos (Arts. 10 y 11):** cuentan de 20 a 199 h, y los aprobados y dictados juntos tienen un tope de 60 puntos. El tope de 70 de los Arts. 8 a 11 sigue.
3. **Caducidad (Arts. 25 y 27), según el decreto:**
   - Los antecedentes de los Arts. 10, 11, 13, 17 y 19 caducan a los 10 años, según la fecha que figura en el certificado. Los Arts. 14, 15, 16 y 18 no se valoran.
   - Los estudios parciales caducan a los 5 años de la última materia aprobada.
   - Títulos, postítulos, posgrados y antigüedad no caducan.
   - Se cuenta desde el cierre de la convocatoria: el último día de inscripción, si el período tiene fechas; si no, desde hoy.
   - Sin fecha cargada no se puede saber, y el antecedente cuenta.
4. **Antigüedad:** al asignarla, el vocal solo ve y carga la antigüedad docente (ítem 02). Las otras son para cargos directivos. El tope sigue en 10 años.

Los valores están en `fixtures/baremo-decreto-257.json`, así que se pueden corregir sin tocar el código.

## Consecuencias

- El estimado del docente y la valoración del vocal usan las mismas reglas: los puntajes ya calculados bajan donde había una segunda acumulación, y pueden cambiar en los cursos de más de 150 h.
- El vocal dijo que vence todo lo que no sea título, postítulo o antigüedad. El decreto no incluye a los Arts. 20 a 24 (libros, publicaciones, comisiones, becas, adscripciones, conceptos), y se siguió el decreto. Los conceptos ya cuentan solo los tres últimos años (Art. 24).
- Queda por confirmar con la Junta si el tope de 60 puntos es para los cursos de los dos artículos juntos o para cada uno.

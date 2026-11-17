# ADR-035: Reclamos del docente

**Estado:** Aceptado, con plazos y límites a confirmar con la Junta
**Fecha:** 2026-11-06
**Relacionado:** historia «Resolver reclamos», ADR-018, ADR-028, ADR-033, ADR-034

## Contexto

El docente tiene que poder reclamar lo que el vocal rechazó y su valoración, y el vocal tiene que resolverlo con una observación, dejando registro y avisándole. En el proyecto no había nada de esto, y cómo funciona el período de reclamos en la Junta todavía no está confirmado.

## Decisión

- **Dos tipos.** Por un antecedente rechazado, y general por la valoración, con texto libre.
- **El de antecedente se resuelve en un paso.** Al aprobar, el vocal elige si queda aceptado o vuelve a revisión.
- **El de valoración no toca nada solo.** Como el texto es libre, el portal no sabe qué dato cambiar: el vocal abre el legajo desde el reclamo, corrige con lo que ya existe y después registra su decisión.
- **Lo resuelve el vocal que hoy tiene la escuela base**, sea o no el que rechazó. Si se va, el reclamo no queda sin nadie.
- **Límites.** Un antecedente se reclama una sola vez; retirar el reclamo no cuenta. Por la valoración hay uno abierto a la vez. Texto obligatorio y hasta 3 PDF de 1 MB.
- **Plazo configurable.** `RECLAMOS_DESDE` y `RECLAMOS_HASTA`; sin fechas se puede reclamar siempre.
- **Con un reclamo abierto no se elimina el antecedente.** El docente puede retirar el reclamo y después eliminarlo.
- **Aviso en la campanita.** El proyecto no tiene envío de correos.
- **La resolución formal es una constancia en pantalla:** decisión, observación, fecha y hora, y vocal. Sin PDF hasta conocer el formato real de la Junta.
- **Solo en PostgreSQL** (`reclamo`, `reclamo_adjunto`, migración 012). Por ahora no hay espejo a Oracle: ver lo relevado abajo.

## Consecuencias

- Los plazos, los adjuntos y quién resuelve salen de decisiones provisorias: se revisan después de la reunión con el vocal.
- Un reclamo de valoración aprobado no garantiza que se haya corregido algo: depende del vocal, y la pantalla se lo recuerda.
- Si un aspirante cambia de escuela base, su reclamo pasa al vocal de la escuela nueva.
- Los PDF de los reclamos se cargan en memoria al arrancar, igual que los del legajo: suma al problema pendiente de los PDF.

## Relevado en Oracle (MT4D, 06/11/2026)

La base de la Junta sí tiene reclamos, y no se conocían al diseñar esto:

- `T_INS_RECLAMOS_JD`: cabecera con empleado, escuela base, convocatoria, estado, motivo, intervención final y tipo. Tiene clave foránea a `T_INSCRIPCIONES_ESCUELABASE_JD` y disparador de historial.
- `T_INS_DETALLES_RECLAMO_JD`: una línea por ítem (títulos, cursos, antigüedad), con la causa del docente, la intervención de la Junta y un estado propio.
- `T_INS_TIPOS_RECLAMO`: dos tipos, «Reclamo LOM provisoria» y «Recurso de reconsideración».

Los datos parecen de prueba: 57 reclamos de 10 docentes, de una sola escuela y de la convocatoria 2021, sin ninguno resuelto. Sirven para ver la estructura, no para saber cómo resuelve la Junta.

**Diferencias con este diseño.** La Junta reclama por rubro y responde por línea; el portal, por antecedente o en general, y resuelve el reclamo entero. Además distingue dos instancias que el portal no tiene. Los dos modelos se pueden traducir: cada reclamo del portal sería una cabecera con una línea. Se decide después de la reunión con el vocal si se adapta el modelo y si se escribe en esas tablas.

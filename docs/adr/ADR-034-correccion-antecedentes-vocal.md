# ADR-034: Corrección de antecedentes por el vocal

**Estado:** Aceptado  
**Fecha:** 2026-11-05  
**Relacionado:** ADR-027 (Dto. 257/19), ADR-029 (tablas de la Junta), ADR-032 (auditoría), ADR-033 (valoración del título)

## Contexto

Si el docente se equivocaba al cargar un antecedente, el vocal solo podía rechazarlo y esperar que lo volviera a cargar. Un error de
tipeo en una fecha o en el promedio frenaba la revisión y, si el antecedente ya estaba aceptado, cambiaba el puntaje sin que nadie
pudiera arreglarlo.

## Decisión

1. **Qué se corrige.** Solo los datos: textos, fechas, horas y números. Los PDF quedan como los subió el docente, porque son el
   respaldo de lo que se corrige.
2. **Qué antecedentes.** Los cargados a mano, en cualquier estado. Los importados de la provincia quedan como vinieron, porque salen
   de un registro oficial, y la antigüedad de la Junta tiene su propia pantalla.
3. **Estado.** No cambia: si estaba pendiente el vocal lo acepta o rechaza, y si estaba aceptado sigue aceptado y el puntaje se
   recalcula con el dato corregido.
4. **Qué ve el docente.** En su legajo, «Corregido por la Junta» con el valor anterior y el nuevo, quién lo hizo, cuándo y el motivo,
   que es obligatorio. Es una marca en la lista: el formulario de carga no cambia.
5. **Reglas.** Las mismas de la carga, con los mismos validadores de `LegajoService`. El título se elige del catálogo y no puede
   repetir otro vigente. Si cambia un nombre elegido con buscador (institución, cargo o establecimiento), su código deja de valer.
6. **Campos.** Una sola tabla en la API (`correccion-antecedente.ts`) arma el formulario del vocal y el detalle de lo que cambió. El
   formulario manda solo lo que cambió y la API ignora cualquier otro campo, como adjuntos o estado.
7. **Guardado.** `correcciones` va dentro del antecedente, en el archivo y en la columna `datos` de PostgreSQL, sin migración. El cambio
   también queda en `auditoria_cambio`, sin los valores (ADR-032).

## Consecuencias

- **Oracle:** el espejo lleva la corrección a la fila del portal en `T_EMP_ITEMS_VARIO_JD`, `T_EMP_TITULOS_EMPLEADO`,
  `T_EMP_ITEMS_CONCEPTO_JD` y `T_EMP_ITEM_EST_PARCIAL_JD`, y a su copia validada en `T_ITEMS_*`, con el usuario del vocal (ADR-029). Solo
  toca antecedentes con corrección y si el dato difiere, así que no pisa lo que la Junta editó en los demás. Si cambia un campo de la
  clave (año o escuela del concepto, título del estudio parcial), la copia validada se borra y se vuelve a cargar.
- La antigüedad declarada va a `T_EMP_ANTIGUEDADES` y el motivo a `COMENT` u `OBSERVACIONES`, hasta donde entra (ADR-029).
- Todavía no llega un título que la Junta ya copió a `T_TITULOS_EMPLEADO`: es el registro oficial y hay que acordarlo con el DBA.
- El docente no puede eliminar lo que el vocal aceptó o corrigió. Lo rechazado sí, porque tiene que poder volver a cargarlo.
- Un título cargado antes de pedir los certificados se puede corregir aunque le falten: la corrección no exige adjuntos.

## Referencias

- Código: `src/server/src/modules/legajo/correccion-antecedente.ts`, `legajo.service.ts` (`corregirDatos`),
  `src/server/src/modules/junta/junta.service.ts`, `src/web/src/app/features/junta/corregir-antecedente-dialog.component.ts`.
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml), `PUT /api/junta/aspirantes/{cuil}/antecedentes/{id}`.
- Casos de prueba: CP-P2-46 y CP-WEB-18 en [`docs/casos-prueba.md`](../casos-prueba.md).

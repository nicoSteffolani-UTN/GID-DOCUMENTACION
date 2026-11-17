# ADR-013: Escuela base — sin paso dedicado en portal nuevo

**Estado:** Aceptado  
**Fecha:** 2026-05-31  
**Actualizado:** 2026-07-06 (confirmación cátedra / JTP)

## Contexto

El sistema legacy exige elegir escuela base antes del Formulario C. Instancia 2 modeló US-02. En relevamiento se propuso no replicar ese paso en el portal nuevo: las escuelas se eligen dentro del Form C (hasta 3, acta junta).

## Decisión

1. No implementar US-02 como módulo obligatorio en el MVP P1.
2. Búsqueda de establecimientos solo en Formulario C.
3. Ruta stub `/escuela-base` eliminada del enrutador; sin enlace en home.

## Consecuencias

- Flujo aspirante más corto: login → legajo docente → Formulario C.
- Vista de vocales (agrupación por escuela base) queda fuera del MVP UI (P2).

## Confirmación

Cátedra y JTP validaron el flujo sin escuela base para el portal nuevo (julio 2026).

## Addendum (25/10/2026)

El aspirante sigue sin elegir escuela base, pero la Junta la necesita (`T_INSCRIPCIONES_JD.ID_EMPRESA_BASE`) y es lo que reparte los legajos entre los vocales. Para el docente da lo mismo cuál es, así que la asigna el sistema al guardar el Formulario C (`asignarEscuelaBase`): la primera escuela elegida, que se conserva mientras siga en la inscripción. No se muestra en el portal. Reemplaza al campo `escuelaBaseId`, que dejaba elegirla. El reparto entre vocales está en el addendum del 25/10 de ADR-018.

## Addendum (27/10/2026): la elige el aspirante

Revierte el addendum anterior. En la devolución del 25/10 el JTP pidió dejar la escuela base como en el sistema de la Junta: el aspirante la elige como referencia para que la Junta agrupe las inscripciones. No hay otra forma de hacer ese agrupamiento. Se vuelve al modelo del commit `de04298`:

- El Formulario C tiene un campo **Escuela base** con el buscador del padrón. Puede ser cualquier escuela del padrón, no necesariamente una de las tres en las que se inscribe. `PUT /api/formulario-c` recibe `escuelaBaseId`. Si el campo no viene, se conserva la escuela base guardada. Si viene `null` o vacío, o el aspirante nunca eligió una, se toma la primera escuela de la inscripción, para que `ID_EMPRESA_BASE` nunca quede vacío. Un ID que no está en el padrón devuelve 400.
- No cambia nada más: la escuela base sigue definiendo qué vocal revisa el legajo (ADR-018) y el espejo la escribe en `T_INSCRIPCIONES_JD.ID_EMPRESA_BASE` (ADR-029).


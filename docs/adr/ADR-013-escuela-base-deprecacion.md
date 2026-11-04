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

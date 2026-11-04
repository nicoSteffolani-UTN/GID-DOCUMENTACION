# ADR-018: Vocal × escuela — pendiente Oracle lab

**Estado:** Pendiente JTP (bloqueado en MT4D)  
**Fecha:** 2026-08-24  
**Relacionado:** US-06, ADR-014, diagrama 19

## Contexto

US-06 asigna escuelas a vocales vía `VocalEscuelaRepository` (`vocal-escuelas.mock.json`: CUIL vocal → `escuelaId[]`). Se buscó vista acta en Oracle lab.

## Discovery MT4D (24/08)

| Objeto | Hallazgo |
|--------|----------|
| Vistas con `VOCAL`, `ACTA`, `ASIGNACION` | No hay acta CUIL×`ID_EMPRESA` usable |
| `T_USUARIOS_JUNTA` | Permisos de sistema, no asignación escuela–vocal |

## Decisión

**No implementar** `OracleVocalAdapter` en lab. Mantener `MockVocalEscuelasAdapter` en todos los modos hasta vista ministerial confirmada.

## Consecuencias

- Demo junta/vocal en prod Oracle **no** refleja asignaciones reales de acta.
- Roles `vocal` en JWT siguen funcionando con fixture local.

## Actualización (13/10)

El fixture usa escuelas ficticias (`ESC-…`) que no existen en el padrón del lab. En `prod` la API ya no lo usa: las escuelas reales con inscripciones se reparten entre los vocales de forma determinística (la misma escuela siempre cae en el mismo vocal), y la junta lo avisa como «asignación de escuelas de ejemplo». En `mock` y en los tests se mantiene el fixture.

## Pregunta para JTP

¿Cuál es la vista oficial de **acta de vocal** (CUIL × código empresa `EE…`) en MT4T/producción?

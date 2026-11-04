# ADR-003: Base de datos Oracle

- **Estado:** Aceptado (pendiente confirmación laboratorio JTP)
- **Fecha:** 2026-05 (Instancia 2)

## Contexto

Persistencia del portal; restricción de stack en sistemas provinciales.

## Decisión

**Oracle** como base de datos objetivo en ambientes acordados con la cátedra/provincia.

## Alternativas consideradas

- PostgreSQL / mocks solo en desarrollo local (a documentar si el JTP lo autoriza).
- Mientras el lab sea de solo lectura, lo que carga el portal se guarda en PostgreSQL (ADR-028).

## Consecuencias

- **Positivo:** Alineación institucional.
- **Negativo:** Setup de laboratorio y migraciones más costosos; dependencia de infraestructura.

## Referencias

- Instancia 2 §11 · Instancia 1 (Oracle citado en procesos)

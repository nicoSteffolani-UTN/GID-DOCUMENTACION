# ADR-006: API REST por defecto

- **Estado:** Aceptado
- **Fecha:** 2026-05 (Instancia 2)

## Contexto

Contrato entre frontend Angular y backend Node.

## Decisión

**REST** como estilo por defecto. GraphQL solo si surge necesidad medible.

## Consecuencias

- **Positivo:** Simplicidad, herramientas conocidas, documentación OpenAPI posible.
- **Negativo:** GraphQL diferido si aparecen consultas muy agregadas.

## Referencias

- Instancia 2 §11

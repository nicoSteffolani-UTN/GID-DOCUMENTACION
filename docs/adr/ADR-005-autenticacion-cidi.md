# ADR-005: Autenticación CiDi, OAuth2 y JWT

- **Estado:** Aceptado
- **Fecha:** 2026-05 (Instancia 2)

## Contexto

Identidad provincial obligatoria (CiDi nivel 2); desarrollo local sin credenciales reales.

## Decisión

- **Producción / MVP académico:** CiDi + OAuth2 + JWT.
- **Desarrollo:** **mock de CiDi** (no reemplaza la integración real acordada para el MVP).

## Consecuencias

- **Positivo:** Permite avanzar US-01 en Sprint 1 sin bloquear por sandbox.
- **Negativo:** Dos caminos de auth a mantener y probar.

## Referencias

- Instancia 2 US-01 · Instancia 1 (proceso autenticación)

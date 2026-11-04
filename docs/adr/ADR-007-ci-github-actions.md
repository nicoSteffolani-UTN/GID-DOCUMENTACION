# ADR-007: CI/CD con GitHub Actions

- **Estado:** Aceptado
- **Fecha:** 2026-05 (Instancia 2)

## Contexto

DoD exige pruebas automáticas y PRs revisados; repositorio en GitHub.

## Decisión

**GitHub Actions** para lint, tests y (futuro) despliegue.

## Estado de implementación

- Workflow `.github/workflows/ci.yml` activo: `verify-docs`, tests API (Jest + supertest), build server, tests web (Karma headless), build Angular.
- Ampliar con lint e2e cuando el equipo lo priorice.

## Consecuencias

- **Positivo:** Integración nativa con el remoto del equipo.
- **Negativo:** Minutos de CI consumidos; mantener pipeline simple al inicio.

## Referencias

- Instancia 2 §11 · `SCM.md` § CI/CD

# ADR-026: Datos del lab Oracle por defecto; mock como alternativa

**Estado:** Aceptado  
**Fecha:** 2026-10-07  
**Relacionado:** ADR-009 (alcance de título), ADR-015/016/019/020 (integraciones del lab), ADR-021 (Docker)

## Contexto

Hasta ahora `INTEGRATION_MODE` valía `mock` por defecto y el lab Oracle se activaba a mano (`.env` con `prod` en local, y un override aparte en Docker). Con alcance, padrón, catálogo, mapa, LOM y alertas ya integrados en solo lectura, el equipo trabaja y demuestra el portal con los datos del lab, y los fixtures quedaron como respaldo para cuando no hay VPN.

## Decisión

- `INTEGRATION_MODE` vale **`prod` por defecto** (`config/env.ts`, `.env.example`, `compose.yaml` y `compose.dev.yaml`).
- Los Compose pasan a la API las credenciales y la conexión del lab desde `src/.env` y montan el dump `ALCANCE_SQL_PATH` si está definido.
- **`mock` es la alternativa explícita:** `INTEGRATION_MODE=mock` en `src/.env` para `npm run dev:server`, o el override `compose.mock.yaml` en Docker.
- Los tests siguen siempre en `mock` (`jest.setup-env.js`), así que CI no necesita VPN.

## Consecuencias

**Positivas**

- Lo que se desarrolla y se demuestra es lo que muestra el lab, sin pasos extra.
- Un solo `src/.env` sirve para local y para Docker.

**Negativas**

- Sin VPN la API no arranca hasta elegir la alternativa `mock`; el error lo indica.
- Lo que sigue simulado aun en `prod` no cambia: login CiDi, SGE, asignación vocal–escuela, simulador, capacitaciones y la persistencia en JSON (ADR-003, ADR-017, ADR-018).

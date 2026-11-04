# ADR-012 — Recomendador de capacitaciones (mock US-12)

## Estado

Aceptado — spike demo / defensa (junio 2026).  
**Addendum 22/09/2026:** el catálogo mock se alimenta de un extracto SIPEC **sin datos personales**; `puntosEstimados = horas × 0,017` (Dto. 257/19 Art. 10, hs ≥ 20).

## Contexto

Se necesita mostrar valor de autogestión docente sugiriendo cursos de capacitación según legajo y puntaje. No hay catálogo ministerial integrado en el MVP.

## Decisión

1. Exponer `GET /api/capacitaciones/recomendaciones` (rol `aspirante`).
2. Catálogo y reglas en `fixtures/capacitaciones-sugeridas.mock.json`.
3. Puerto `CapacitacionRecomendadorPort`; implementación `MockCapacitacionRecomendadorAdapter`.
4. Lógica mock:
   - Si puntaje LOM tiene `capacitaciones` por debajo del umbral (12), sugerir hasta 3 cursos.
   - Si el legajo no tiene categoría `cursos_capacitaciones`, sugerir cursos genéricos.
   - Cada curso incluye `puntosEstimados` (mock) hacia el escalafón LOM.
5. UI en `/capacitaciones` con banner de vista preliminar y chips “+X pts LOM”.

## Contrato de respuesta

```json
{
  "aviso": "string",
  "sugerencias": [
    {
      "motivo": "Tu componente de capacitaciones en la LOM…",
      "cursos": [
        {
          "id": "CAP-001",
          "nombre": "...",
          "modalidad": "virtual",
          "horas": 40,
          "puntosEstimados": 2.5
        }
      ]
    }
  ]
}
```

## Limitaciones

- Cursos ficticios; sin inscripción ni certificación.
- Puntos estimados mock; no son el baremo oficial.
- Umbral y reglas hardcodeadas en fixture hasta relevamiento PO.

## Consecuencias

- Enchufe futuro: reemplazar adapter o enriquecer fixture sin cambiar contrato HTTP.
- Tests en `capacitaciones.test.ts` y smoke en `demo-flow.test.ts`.

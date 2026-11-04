# ADR-011 — Simulador de puntaje LOM (mock US-11)

## Estado

Aceptado — spike demo / defensa (junio 2026).  
**Addendum 05/10/2026:** la pantalla `/simulador` pasó a ser el simulador de carrera docente ([ADR-024](./ADR-024-simulador-carrera-docente.md)). Este endpoint (`/api/simulador/ascenso`) se mantiene porque lo usa el asistente.  
**Addendum 06/09/2026:** se corrige el alcance. El simulador proyecta **puntaje en la lista de orden de méritos (LOM)**, no requisitos de cargos de gestión (Director/Supervisor). Esos cargos (Form D/I) quedan fuera del MVP según Instancia 1.  
**Addendum 22/09/2026:** los `puntosEstimados` de los escenarios se derivan de cursos tipo SIPEC (sin PII) con la fórmula del **Dto. 257/19 Art. 10** (`horas × 0,017`, solo cursos ≥ 20 h). Sigue siendo estimación mock, no orden oficial.

## Contexto

Instancia 1/2 piden un simulador de obtención de puntaje / posición en la LOM, alimentado por capacitaciones que suman al escalafón. El spike inicial había implementado por error un checklist de cargos jerárquicos.

## Decisión

1. Exponer `GET /api/simulador/ascenso` (rol `aspirante`; se mantiene la ruta por compatibilidad).
2. Fixture `fixtures/simulador-lom.mock.json` con cursos y `puntosEstimados`.
3. Puerto `AscensoSimuladorPort.getSimulacion(cuil)` → puntaje actual + escenarios (base, delta, proyectado).
4. UI en `/simulador` con banner: datos mock, no es orden oficial.

## Contrato de respuesta

```json
{
  "aviso": "string",
  "puntajeActual": { "total": 66.5, "ciclo": "2026" },
  "escenarios": [
    {
      "id": "un-curso",
      "titulo": "Con Evaluación por competencias…",
      "descripcion": "…",
      "cursosAplicados": [{ "id": "CAP-001", "nombre": "…", "puntosEstimados": 2.5 }],
      "puntajeBase": 66.5,
      "delta": 2.5,
      "puntajeProyectado": 69.0
    }
  ]
}
```

## Limitaciones

- Deltas mock; no representan la fórmula oficial LOM.
- Sin ranking provincial real ni cupos.
- `INTEGRATION_MODE=prod` puede usar LOM parcial Oracle como base; los deltas siguen mock.

## Consecuencias

- Alineado a Inst 1/2 y al flujo aspirante (puntaje + capacitaciones).
- Banner y ADR mitigan interpretación como “oficial”.

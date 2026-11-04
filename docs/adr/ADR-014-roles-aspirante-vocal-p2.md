# ADR-014: Roles aspirante/vocal y módulos P2 (US-05, US-06)

**Estado:** Aceptado  
**Fecha:** 2026-07-06

## Contexto

Planning 2 (Instancia 2) exige:

- **US-05:** consulta de puntaje de inscripción solo para el aspirante autenticado.
- **US-06:** revisión de aspirantes por escuela para vocales de junta.

P1 implementó solo el flujo aspirante. ADR-013 deprecó escuela base del aspirante; las escuelas se eligen en Formulario C.

## Decisión

1. **Roles en JWT:** `aspirante` | `vocal` con middleware `requireRole` en backend y guards en frontend.
2. **Redirect post-login:** vocal → `/junta`; aspirante → `/home`.
3. **US-06 — escuelas del vocal:** fixture `vocal-escuelas.mock.json` (asignación CUIL vocal → escuelaId[]). Los aspirantes se cruzan por `formulario.postulaciones` en el store por CUIL.
4. **US-06 — revisión:** módulo `/api/junta` con aceptar/rechazar antecedentes; estado `RECHAZADO` + `observacionRechazo` visible en legajo del aspirante (solo lectura).
5. **US-05 — puntaje:** fixture `puntaje.mock.json` keyed por CUIL del token; endpoint `GET /api/puntaje/inscripcion` sin parámetro de CUIL ajeno.

## Consecuencias

- Aislamiento por rol: aspirante 403 en `/api/junta`; vocal 403 en legajo, Form C, validador y puntaje.
- Integración motor LOM y acta escuela–vocal quedan para producción; mocks permiten demo y defensa.
- Diagramas 05, 11, 18 y secuencias 20–21 documentan el flujo P2.

## Trazabilidad

| US | API | Pantalla |
|----|-----|----------|
| US-05 | `GET /api/puntaje/inscripcion` | `/puntaje` |
| US-06 | `GET/POST /api/junta/*` | `/junta` |
| Observaciones rechazo | (lectura en `GET /api/legajo`) | `/legajo` columna observaciones |

## Usuarios demo

| CUIL | Rol | Uso |
|------|-----|-----|
| 20123456789 | aspirante | Puntaje mock + Form C |
| 20111111111 | aspirante | Sin valoración puntaje |
| 27999888777 | vocal | ESC-001, ESC-002 |
| 27111222333 | vocal | ESC-003 (aislamiento) |

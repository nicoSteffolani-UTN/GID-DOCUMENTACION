# [ID: US-76] Parametrización administrativa de períodos de convocatoria anual desde el portal

- **Épica:** Sin Épica (Transversal - Operación y Gobernanza de Junta)
- **Prioridad:** P2 (Media / Implementada)
- **Estimación:** 3 SP
- **Rol / Actor:** Administrador de la Junta / Usuario de Sistemas (`ADR-036`, `ADR-038`)

## Descripción (Narrativa)
> **Como** Administrador de la Junta / Usuario de Sistemas,  
> **quiero** configurar los períodos de apertura o cierre de la inscripción anual y de reclamos desde la pantalla de administración del portal (`Junta → Períodos`),  
> **para** acompasar el comportamiento del sistema con el calendario oficial fijado por resolución ministerial sin depender de modificaciones técnicas en el servidor.

## Reglas de Negocio y Conexiones Externas
- **Pantalla y Endpoint (`ADR-036`, `ADR-038`):** Pantalla `Junta → Períodos` (`junta-periodos.component`) y endpoints `GET` y `PUT /api/junta/periodos`.
- **Modos de Período:** Cada período (`inscripcion` y `reclamos`) puede definirse como `ABIERTO`, `CERRADO` o `FECHAS` (con rangos válidos `desde` y `hasta`).
- **Persistencia y Prioridad:** Lo guardado se almacena en PostgreSQL (`periodos_convocatoria`) y manda sobre los valores iniciales de `.env`. Afecta automáticamente la habilitación del Formulario C, el módulo de reclamos y el cómputo de caducidades normativas (`ADR-039`).

## Criterios de Aceptación
1. **Debe** exponer la pantalla `Junta → Períodos` permitiendo al Administrador de la Junta conmutar el estado de inscripción y reclamos entre modo `ABIERTO`, `CERRADO` o `FECHAS`.
2. **Debe** validar que en modo `FECHAS` la fecha `desde` no sea posterior a la fecha `hasta` y que ambas sean fechas calendario válidas.
3. **Debe** reflejar de forma inmediata el estado vigente en el Formulario C, los reclamos docentes y el Asistente Virtual.
4. **Debe** registrar quién y cuándo modificó cada período y dejar asentada la acción en la bitácora de auditoría (`ADR-032`).
5. **Debe** restringir la modificación exclusivamente a usuarios con rol de Administrador de la Junta (`ADMINISTRADORES_CUIL`).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar establecer el período de inscripción en estado `ABIERTO` o `FECHAS` válidas y verificar que el Formulario C permite la edición y confirmación de postulaciones. *(Pasa)*
- **CP-02:** Probar conmutar la inscripción a `CERRADO` y verificar que el Formulario C bloquea el alta/edición pasando a modo de solo lectura. *(Pasa)*
- **CP-03:** Probar ingresar a modificar períodos con un usuario vocal estándar o docente y verificar el bloqueo por permisos insuficientes (HTTP 403 Forbidden). *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Control reactivo de estados temporales y validación de rangos de fechas. |
| **Esfuerzo** | Bajo | Componente `junta-periodos`, validaciones en `periodos.service.ts` y tabla `periodos_convocatoria` (`ADR-036`). |
| **Incertidumbre** | Nula | Implementado y cubierto con pruebas unitarias e integración. |

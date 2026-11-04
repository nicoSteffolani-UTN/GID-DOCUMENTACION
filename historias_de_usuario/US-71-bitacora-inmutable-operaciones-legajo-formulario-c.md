# [ID: US-71] Bitácora inmutable de operaciones sobre el legajo digital y el Formulario C

- **Épica:** E14 - Gobernanza, Seguridad y Auditoría Inmutable
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Sistema / Autoridad de Auditoría Ministerial

## Descripción (Narrativa)
> **Como** autoridad de la Junta de Clasificación Secundaria,  
> **quiero** que toda alta, baja o sincronización de antecedentes en el legajo y cada confirmación de inscripción del Formulario C generen un asiento automático en la bitácora de cambios del portal,  
> **para** preservar el respaldo histórico completo de cada presentación realizada por los docentes aspirantes.

## Reglas de Negocio y Conexiones Externas
- **Trazabilidad Transaccional (`ADR-032`):** Registro automático en la tabla `auditoria_cambio` en PostgreSQL bajo modalidad `APPEND-ONLY`, acompañando los lineamientos de resguardo documental de la Junta de Clasificación.

## Criterios de Aceptación
1. **Debe** registrar automáticamente un asiento en `auditoria_cambio` cuando un docente aspirante:
   - Agrega manualmente un antecedente a su legajo (`ALTA_ANTECEDENTE`).
   - Elimina un antecedente en estado pendiente (`BAJA_ANTECEDENTE`).
   - Ejecuta la sincronización de antecedentes provinciales (`SINCRONIZACION_LEGAJO`).
   - Guarda o confirma su inscripción anual en el Formulario C (`CONFIRMACION_FORMULARIO_C`).
2. **Debe** consignar en cada registro: marca temporal exacta del servidor, CUIL del actor ejecutor, rol, entidad afectada (`legajo`, `formulario_c`, `interes`), identificador del recurso y detalle resumido del cambio en formato estructurado (`JSONB`).
3. **Debe** garantizar la inmutabilidad de los asientos registrados impidiendo cualquier alteración retroactiva.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar cargar un antecedente en `/legajo` y confirmar un Formulario C en `/formulario-c`, verificando que en `auditoria_cambio` se registran ambas operaciones con su timestamp, CUIL y detalle del recurso. *(Pasa)*
- **CP-02:** Probar eliminar un antecedente en estado `Pendiente` y comprobar que queda asentado el evento `BAJA_ANTECEDENTE` con el identificador del ítem dado de baja. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Instrumentación transversal en los casos de uso de escritura de `legajo` y `formulario-c` hacia PostgreSQL. |
| **Esfuerzo** | Medio | Servicio de registro en `auditoria_cambio` y serialización de metadatos de la operación. |
| **Incertidumbre** | Nula | Esquema relacional definido en ADR-032. |

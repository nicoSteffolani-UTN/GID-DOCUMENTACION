# [ID: US-72] Registro auditable de dictámenes de validación y rechazo emitidos por Vocales

- **Épica:** E14 - Gobernanza, Seguridad y Auditoría Inmutable
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Sistema / Vocal de Junta

## Descripción (Narrativa)
> **Como** miembro de la Junta de Clasificación Secundaria,  
> **quiero** que cada acto de validación o rechazo de un antecedente quede asentado con la identificación del vocal interviniente, la escuela de contexto, el CUIL del aspirante evaluado, la fecha/hora y el motivo del dictamen,  
> **para** respaldar institucionalmente las actuaciones de cada vocal conforme a los procedimientos administrativos vigentes.

## Reglas de Negocio y Conexiones Externas
- **Respaldo del Acto Administrativo (`ADR-032`):** Toda resolución emitida desde `/junta` genera un asiento inmutable en `auditoria_cambio` vinculando al vocal evaluador con el antecedente dictaminado.

## Criterios de Aceptación
1. **Debe** registrar un asiento en `auditoria_cambio` cada vez que un vocal acepta (`VALIDACION_ANTECEDENTE`) o rechaza (`RECHAZO_ANTECEDENTE`) un certificado desde la bandeja de Junta.
2. **Debe** incluir en el registro auditable: el CUIL del vocal que emitió el dictamen, el CUIL del docente titular del legajo, el identificador del antecedente, el código de la escuela (`escuelaId`), el estado anterior (`Pendiente`), el nuevo estado (`Validado` o `Rechazado`), el motivo de rechazo (cuando aplique) y la marca temporal del servidor.
3. **Debe** mantener el registro en carácter estrictamente `APPEND-ONLY` dentro de la base de datos PostgreSQL del portal.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar que cuando un vocal acepta un antecedente en `/junta`, se genera el asiento correspondiente en `auditoria_cambio` con el CUIL del vocal, el CUIL del aspirante y el cambio de estado a `Validado`. *(Pasa)*
- **CP-02:** Probar que cuando un vocal rechaza un antecedente con motivo fundado, el registro de auditoría conserva el motivo exacto y los identificadores de ambas partes. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Invocación al servicio de auditoría dentro de las transacciones de aceptación y rechazo del módulo `junta`. |
| **Esfuerzo** | Bajo | Integración entre `junta.service.ts` y `auditoria.service.ts`. |
| **Incertidumbre** | Nula | Implementado y cubierto por pruebas en el backend. |

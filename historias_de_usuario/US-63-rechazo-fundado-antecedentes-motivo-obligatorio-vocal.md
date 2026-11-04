# [ID: US-63] Rechazo fundado de antecedentes con motivo obligatorio por el Vocal de Junta

- **Épica:** E12 - Gestión y Fiscalización para Vocales de Junta
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Vocal de Junta

## Descripción (Narrativa)
> **Como** vocal evaluador de la Junta,  
> **quiero** rechazar un antecedente que no cumpla con la normativa vigente ingresando obligatoriamente el motivo fundamentado de la observación,  
> **para** dejar constancia administrativa del dictamen y notificar con claridad al docente aspirante por qué dicho certificado no fue computado.

## Reglas de Negocio y Conexiones Externas
- **Regla de Fundamentación Obligatoria:** No se admite ningún rechazo sin justificación escrita; el campo `motivo` debe tener una longitud mínima estricta de **10 caracteres** (`POST /api/junta/aspirantes/:cuil/antecedentes/:id/rechazar` con body `{ escuelaId, motivo }`).

## Criterios de Aceptación
1. **Debe** solicitar al vocal la redacción de un **motivo de rechazo** al presionar la opción «Rechazar» sobre un antecedente.
2. **Debe** validar en el frontend y en el backend que el motivo ingresado posea **al menos 10 caracteres** no vacíos (`HTTP 400` si es menor o nulo).
3. **Debe** actualizar el estado del antecedente a **`Rechazado`**, almacenando el texto del motivo, la fecha/hora del acto y el CUIL del vocal evaluador.
4. **Debe** excluir el antecedente rechazado del cómputo de puntaje validado del aspirante y exponer la observación de forma inmediata en la bandeja de rechazos del docente (`/legajo`).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar rechazar un antecedente ingresando un motivo válido de más de 10 caracteres (ej. «Certificado sin firma de autoridad competente ni resolución ministerial») y verificar que pasa a estado `Rechazado` y se guarda la observación. *(Pasa)*
- **CP-02:** Probar intentar confirmar un rechazo con el campo de motivo vacío o con un texto corto como «No vale» (7 caracteres) y verificar que el sistema bloquea la operación exigiendo un mínimo de 10 caracteres. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Validación de regla de negocio sobre el motivo, persistencia del dictamen y notificación en las observaciones del legajo. |
| **Esfuerzo** | Medio | Modal/formulario de motivo de rechazo en `/junta` y endpoint `POST .../rechazar`. |
| **Incertidumbre** | Nula | Regla de negocio verificada por tests automatizados de backend y frontend. |

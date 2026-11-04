# [ID: US-16] Consulta detallada de observaciones y motivos de rechazo emitidos por el Vocal

- **Épica:** E03 - Gestión del Legajo Digital y Antecedentes
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar de forma clara el motivo específico, la fecha de revisión y el detalle por el cual un vocal de la Junta rechazó u observó alguno de mis antecedentes,  
> **para** comprender la causa normativa del rechazo y poder subsanar el documento o presentar el reclamo correspondiente con información precisa.

## Reglas de Negocio y Conexiones Externas
- **Regla de Transparencia Administrativa:** Todo acto de rechazo de un antecedente por parte de un vocal requiere un motivo fundamentado obligatorio (`motivoRechazo` ≥ 10 caracteres), el cual se expone íntegramente al titular del legajo (`GET /api/legajo/observaciones`).

## Criterios de Aceptación
1. **Debe** destacar visualmente en `/legajo` cualquier antecedente cuyo estado sea **`Rechazado`**.
2. **Debe** mostrar una bandeja o sección específica de **«Observaciones de rechazo»** (`GET /api/legajo/observaciones`) que consolide todos los ítems observados del docente.
3. **Debe** detallar para cada observación: denominación del antecedente, categoría, institución, fecha del dictamen y el texto completo de la observación/motivo redactado por el vocal evaluador.
4. **Debe** orientar al docente desde la observación sobre cómo subsanar el error (volver a cargar el documento legible/completo si la ventana está abierta, o iniciar un reclamo formal en período de exhibición de LOM).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar que luego de que un vocal rechaza un antecedente de un aspirante indicando el motivo «El certificado adjunto no consigna el número de resolución ministerial ni carga horaria», el aspirante visualiza exactamente ese motivo y fecha en su vista de `/legajo`. *(Pasa)*
- **CP-02:** Probar consultar la sección de observaciones con un docente que tiene todos sus antecedentes `Validados` y verificar que se muestra el estado sin observaciones pendientes. *(Pasa)*
- **CP-03:** Probar verificar que un aspirante solo recibe las observaciones correspondientes a su propio CUIL. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consulta filtrada de antecedentes en estado `Rechazado` con proyección de metadatos de revisión. |
| **Esfuerzo** | Bajo | Endpoint `/api/legajo/observaciones` y panel de alertas de observaciones en el componente de legajo. |
| **Incertidumbre** | Nula | Integración directa con el flujo de dictamen del vocal (US-06 / E12). |

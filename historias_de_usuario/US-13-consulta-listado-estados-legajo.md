# [ID: US-13] Consulta del listado del legajo y seguimiento de estados de validación

- **Épica:** E03 - Gestión del Legajo Digital y Antecedentes
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar el listado completo de los títulos y antecedentes de mi legajo junto con el estado de revisión actual de cada uno (`Pendiente`, `Validado` o `Rechazado`),  
> **para** hacer un seguimiento transparente de qué documentación ya fue aprobada por la Junta de Clasificación y cuál se encuentra en espera de dictamen.

## Reglas de Negocio y Conexiones Externas
- **Máquina de Estados de Antecedente:** `Pendiente` (cargado por el docente, en espera de revisión del vocal), `Validado` (aprobado por un vocal de Junta, computa puntaje oficial), `Rechazado` (observado con motivo fundado por el vocal, no computa puntaje).

## Criterios de Aceptación
1. **Debe** listar en `/legajo` (`GET /api/legajo`) todos los antecedentes del docente mostrando: Categoría, Denominación, Institución emisora, Fecha de emisión, Horas reloj (si aplica), Origen (`manual` / `provincia`) y Estado actual.
2. **Debe** identificar cada estado mediante distintivos visuales claros (etiquetas de color e ícono accesible para `Pendiente`, `Validado` y `Rechazado`).
3. **Debe** permitir filtrar o agrupar la vista del legajo por categoría de antecedente y por estado de validación.
4. **Debe** mostrar contadores resumidos en la cabecera del legajo con el total de antecedentes cargados, cuántos están validados, cuántos pendientes y cuántos rechazados.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `/legajo` con un docente que posee antecedentes en los tres estados (`Pendiente`, `Validado`, `Rechazado`) y verificar que cada ítem muestra su insignia correspondiente y los contadores coinciden. *(Pasa)*
- **CP-02:** Probar aplicar el filtro de estado `Pendiente` y comprobar que la tabla muestra únicamente los certificados pendientes de evaluación. *(Pasa)*
- **CP-03:** Probar ingresar a `/legajo` con un usuario sin antecedentes cargados y verificar que se presenta el estado vacío con la invitación a cargar o sincronizar antecedentes. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consulta estructurada del legajo del usuario autenticado y filtrado en interfaz. |
| **Esfuerzo** | Bajo | Componente de listado con filtros y badges de estado en Angular. |
| **Incertidumbre** | Nula | Estados del dominio definidos en el diagrama de estados del legajo. |

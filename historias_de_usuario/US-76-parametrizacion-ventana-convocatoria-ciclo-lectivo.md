# [ID: US-76] Parametrización administrativa de ventana de convocatoria anual y ciclo lectivo

- **Épica:** Sin Épica (Transversal - Operación e Interoperabilidad)
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Administrador / Dirección General de Carrera Docente

## Descripción (Narrativa)
> **Como** administrador operativo del sistema,  
> **quiero** configurar los parámetros del ciclo lectivo vigente y el estado de apertura o cierre de la ventana de inscripción anual (`INSCRIPCION_ABIERTA`),  
> **para** acompasar el comportamiento del portal con las fechas oficiales fijadas por resolución ministerial en el calendario anual docente.

## Reglas de Negocio y Conexiones Externas
- **Gobierno del Ciclo Anual (Diagrama 13):** El parámetro `INSCRIPCION_ABIERTA` (y el año del ciclo lectivo activo) determina si el módulo de Formulario C acepta nuevas presentaciones/modificaciones o si opera en modo de solo consulta.

## Criterios de Aceptación
1. **Debe** centralizar la lectura del ciclo lectivo activo y del estado de apertura de la convocatoria anual (`INSCRIPCION_ABIERTA`).
2. **Debe** reflejar cualquier cambio en el parámetro de apertura de convocatoria de forma inmediata en el Panel de Inicio (`/home`) y en las validaciones del módulo de Formulario C (`/formulario-c`).
3. **Debe** restringir la modificación de estos parámetros exclusivamente a configuración del servidor o perfil administrativo autorizado.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar establecer el parámetro de convocatoria en estado abierto y verificar que el portal habilita la edición y confirmación del Formulario C para el ciclo lectivo configurado. *(Pasa)*
- **CP-02:** Probar conmutar el parámetro a convocatoria cerrada y verificar que el portal pasa a modo de consulta de inscripción y exhibe el aviso de período cerrado. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Configuración centralizada de estado de ciclo lectivo consumida por backend y frontend. |
| **Esfuerzo** | Bajo | Variables de configuración y exposición de estado en el endpoint de contexto/ciclo. |
| **Incertidumbre** | Nula | Documentado en el diagrama de estados de inscripción anual. |

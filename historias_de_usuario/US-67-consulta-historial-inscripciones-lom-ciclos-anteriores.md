# [ID: US-67] Consulta de historial de inscripciones, postulaciones y resultados de LOM de ciclos anteriores

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P3 (Baja / Evolución)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** consultar mi historial de inscripciones de Formulario C y mis resultados definitivos de LOM de ciclos lectivos anteriores,  
> **para** llevar un seguimiento de la evolución plurianual de mi puntaje y descargar mis comprobantes históricos cuando los necesite.

## Reglas de Negocio y Conexiones Externas
- **Alcance Funcional (Instancia 2 §19.13):** Consulta histórica de solo lectura agrupada por año/ciclo lectivo (`T_DETALLES_INSC_JD` e inscripciones históricas).

## Criterios de Aceptación
1. **Debe** listar los registros históricos del docente agrupados por ciclo lectivo (en orden descendente desde el más reciente).
2. **Debe** detallar para cada ciclo lectivo anterior: las escuelas y asignaturas inscriptas, el puntaje definitivo alcanzado y la posición en el orden de mérito si estuviera registrada.
3. **Debe** permitir descargar en formato PDF los comprobantes de inscripciones confirmadas en el portal de períodos previos.
4. **Debe** presentar toda la información histórica en modo exclusivo de solo lectura.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar un ciclo lectivo anterior en el filtro de historial y verificar que se despliegan las escuelas, materias y puntajes de ese año. *(Pasa)*
- **CP-02:** Probar consultar el historial con un docente ingresante en su primer ciclo lectivo y verificar que se muestra el aviso «No registrás inscripciones de ciclos lectivos anteriores» sin errores. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consulta filtrada por CUIL y año/ciclo lectivo sobre tablas de inscripciones. |
| **Esfuerzo** | Bajo | Vista de historial con selector de ciclo lectivo en Angular. |
| **Incertidumbre** | Nula | Datos de solo lectura. |

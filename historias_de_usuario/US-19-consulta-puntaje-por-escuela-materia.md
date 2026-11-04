# [ID: US-19] Consulta de puntaje discriminado por escuela y espacio curricular inscripto

- **Épica:** E04 - Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar mi puntaje de inscripción discriminado por cada establecimiento educativo y asignatura/cargo en los que me inscribí o evalúo inscribirme,  
> **para** distinguir las diferencias de puntuación cuando mi título reviste carácter Docente en una materia y Habilitante o Supletorio en otra.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio:** Un mismo docente puede tener distinto puntaje total según la asignatura que aspire a dictar, dado que el alcance de su título puede ser `Docente` (18 pts) para una materia troncal y `Habilitante` (12 pts) o `Supletorio` (6 pts) para otra afín.
- **Conexión de Datos:** Cruce entre las inscripciones del docente (`T_DETALLES_INSC_JD` / Formulario C) y el catálogo de alcances de títulos.

## Criterios de Aceptación
1. **Debe** listar en la vista de puntaje las escuelas y materias/cargos vinculados a la inscripción del docente.
2. **Debe** mostrar para cada combinación Escuela + Materia el carácter del título en esa asignatura (`Docente`, `Habilitante` o `Supletorio`) y el puntaje resultante específico.
3. **Debe** permitir seleccionar una materia o escuela de la lista para inspeccionar cómo varía el rubro de título base en el desglose detallado.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar consultar el puntaje de un docente inscripto en dos materias con diferente alcance de título (ej. Matemática como Docente y Física como Habilitante) y verificar que se exhibe la diferencia de puntos en cada fila. *(Pasa)*
- **CP-02:** Probar seleccionar una fila de Escuela + Materia y comprobar que el detalle de rubros refleja el puntaje correspondiente a ese espacio curricular. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Asociación entre el puntaje base del legajo y la categoría de alcance específica de cada materia. |
| **Esfuerzo** | Medio | Proyección por escuela/asignatura en el endpoint de puntaje y tabla interactiva en Angular. |
| **Incertidumbre** | Baja | Datos disponibles en las tablas de detalle de inscripción del laboratorio Oracle (`T_DETALLES_INSC_JD`). |

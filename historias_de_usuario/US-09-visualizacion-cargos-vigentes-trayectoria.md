# [ID: US-09] Visualización de cargos vigentes e historial de trayectoria activa en el perfil

- **Épica:** E02 - Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante en ejercicio,  
> **quiero** visualizar en mi perfil el listado de mis cargos u horas cátedra vigentes y mi trayectoria de desempeños registrados en la provincia,  
> **para** corroborar mi situación de revista actual (titular, interino o suplente) y los establecimientos donde presto o presté servicios.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Vistas de cargos y conceptos directivos/docentes en `PEOPLENET` (`M4CBA_VW_CONC_DIR` y `M4CBA_VW_CONSE_CAR`, ADR-030).

## Criterios de Aceptación
1. **Debe** listar en la sección de trayectoria del perfil los cargos y horas cátedra vigentes del docente, indicando establecimiento educativo, asignatura/cargo, carácter o situación de revista y carga horaria.
2. **Debe** exponer el historial de desempeños anteriores registrados en la fuente oficial, ordenado cronológicamente desde el más reciente.
3. **Debe** presentar un estado vacío amigable («No se registran cargos activos en el padrón provincial») cuando el aspirante aún no posea designaciones vigentes.
4. **Debe** mostrar los datos en carácter estrictamente informativo y de solo lectura.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar visualizar la sección de cargos vigentes de un docente en actividad y comprobar que se listan el establecimiento, la materia/cargo y la situación de revista. *(Pasa)*
- **CP-02:** Probar visualizar la sección con un aspirante sin cargos vigentes y verificar que se muestra el mensaje informativo sin errores de renderizado. *(Pasa)*
- **CP-03:** Probar verificar que el listado ordena los cargos vigentes por encima de las bajas históricas. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consulta y normalización de registros de historia laboral desde vistas relacionales de `PEOPLENET`. |
| **Esfuerzo** | Medio | Modelado de DTOs de trayectoria y tarjetas/tabla de cargos en la vista de perfil. |
| **Incertidumbre** | Baja | Vistas identificadas y documentadas en ADR-030. |

# [ID: US-24] Búsqueda inversa de títulos habilitantes por espacio curricular o cargo

- **Épica:** E05 - Validador de Alcance de Títulos y Competencias
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** docente aspirante o vocal de la Junta de Clasificación,  
> **quiero** buscar una asignatura o cargo específico del nivel secundario y consultar todos los títulos que poseen alcance para dictarla ordenados por categoría reglamentaria,  
> **para** verificar qué titulaciones son admitidas en un espacio curricular determinado y planificar formación complementaria o auditar inscripciones.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Consulta inversa sobre el catálogo de alcances de títulos de Oracle Lab por código o nombre de asignatura/cargo.

## Criterios de Aceptación
1. **Debe** permitir seleccionar o buscar una asignatura o cargo del padrón curricular de nivel secundario.
2. **Debe** listar los títulos oficiales habilitados para esa asignatura agrupados u ordenados por categoría (`Docente`, `Habilitante`, `Supletorio`).
3. **Debe** resaltar en la lista si alguno de los títulos mostrados coincide con los títulos que el usuario autenticado ya posee en su propio legajo.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar buscar la asignatura «Matemática» (código 320) y verificar que se listan los títulos con alcance Docente, Habilitante y Supletorio para dicha materia. *(Pasa)*
- **CP-02:** Probar que si el docente autenticado posee uno de los títulos de la lista, el sistema lo destaque visualmente como «En tu legajo». *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consulta filtrada por código de materia sobre el repositorio de alcances. |
| **Esfuerzo** | Bajo | Pestaña o filtro de búsqueda por materia en el componente del validador. |
| **Incertidumbre** | Nula | Datos contenidos en la misma estructura de alcances ya integrada. |

# [ID: US-22] Consulta del catálogo oficial de títulos y búsqueda de competencias habilitadas

- **Épica:** E05 - Validador de Alcance de Títulos y Competencias
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** buscar mi título de grado o profesorado dentro del catálogo oficial de la Provincia y consultar todas las asignaturas y cargos de nivel secundario para los que me habilita,  
> **para** conocer de antemano en qué espacios curriculares puedo inscribirme y con qué carácter legal antes de armar mi Formulario C.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Catálogo oficial de Alcances de Títulos del Ministerio de Educación de Córdoba en el laboratorio Oracle (74.765 combinaciones título–materia–carácter relevadas, ADR-009, y documento `ALCANCES-DE-TITULOS-LOM-2027-08-05-2026.pdf`).
- **Categorías Reglamentarias:** `Docente` (18 pts), `Habilitante` (12 pts) y `Supletorio` (6 pts).

## Criterios de Aceptación
1. **Debe** ofrecer un buscador autocompletable o filtrable de títulos oficiales (`/validador` / `GET /api/validador/alcances`) que permita buscar por denominación parcial sin distinción de mayúsculas, minúsculas ni tildes.
2. **Debe** precargar por defecto el título o títulos que el docente ya tenga declarados y validados en su legajo o perfil.
3. **Debe** listar todas las asignaturas y cargos en los que dicho título tiene competencia reconocida para el nivel secundario.
4. **Debe** mostrar junto a cada asignatura el carácter del alcance (`Docente`, `Habilitante` o `Supletorio`) y su equivalente en puntos de título base según el Decreto 257/19.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar buscar el título «PROFESOR DE COMPUTACION Y MATEMATICA» y verificar que el sistema lista sus asignaturas habilitadas (como Matemática, código 320) junto con el carácter `Docente`. *(Pasa)*
- **CP-02:** Probar buscar un término parcial sin tildes (ej. «computacion» o «biologia») y comprobar que el filtro devuelve las coincidencias normalizadas del catálogo. *(Pasa)*
- **CP-03:** Probar buscar un texto inexistente en el nomenclador oficial y verificar que se informa «No se encontraron títulos que coincidan con la búsqueda» sin errores. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Indexación y consulta eficiente sobre el catálogo de más de 74.000 combinaciones en Oracle Lab. |
| **Esfuerzo** | Medio | Endpoint de consulta con normalización de texto y vista de buscador de alcances en Angular. |
| **Incertidumbre** | Baja | Estructura del nomenclador de alcances verificada contra el laboratorio Oracle (ADR-009). |

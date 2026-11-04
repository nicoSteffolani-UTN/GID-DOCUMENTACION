# [ID: US-27] Consulta y búsqueda del padrón oficial de escuelas secundarias y su oferta curricular

- **Épica:** E06 - Convocatoria Anual e Inscripción en Formulario C
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** buscar establecimientos educativos estatales dentro del padrón oficial de la Provincia filtrando por nombre, código de empresa/CUE, departamento o localidad y consultar las materias y cargos disponibles en cada uno,  
> **para** seleccionar con exactitud las escuelas donde deseo postularme en la convocatoria anual del Formulario C.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Padrón oficial de establecimientos y catálogo curricular para el Formulario C desde el laboratorio Oracle (ADR-015) o fixture homologado en modo simulado.

## Criterios de Aceptación
1. **Debe** proveer en `/formulario-c` (`GET /api/formulario-c/escuelas` / catálogo) un buscador de establecimientos que permita filtrar por nombre de escuela (ej. IPEM, IPEA, IPET), código identificador, departamento y localidad.
2. **Debe** mostrar para cada establecimiento su denominación oficial, código identificatorio, departamento, localidad y domicilio.
3. **Debe** cargar y listar la oferta de asignaturas y cargos docentes habilitados para inscripción en cada establecimiento seleccionado.
4. **Debe** indicar en el listado de materias de la escuela el alcance que posee el docente en cada una (integrado con el validador de alcances).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar buscar establecimientos por departamento (ej. «CAPITAL» o «COLON») o por nombre parcial y verificar que se listan las escuelas reales del padrón con sus datos de ubicación. *(Pasa)*
- **CP-02:** Probar seleccionar una escuela del buscador y verificar que se despliega el catálogo de asignaturas y cargos disponibles para ese establecimiento. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consulta optimizada del padrón de escuelas y su oferta de espacios curriculares en Oracle Lab (ADR-015). |
| **Esfuerzo** | Medio | Endpoints de catálogo de Formulario C y selectores con búsqueda en Angular. |
| **Incertidumbre** | Baja | Vistas del padrón conectadas y verificadas mediante script contra Oracle Lab. |

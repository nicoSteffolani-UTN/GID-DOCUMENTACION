# [ID: US-68] Visualización de métricas y percentiles anonimizados de competitividad regional

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P3 (Baja / Evolución)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar indicadores estadísticos anonimizados que comparen mi puntaje frente a los promedios y percentiles de mi asignatura y departamento,  
> **para** dimensionar mi posicionamiento relativo en la región sin que se expongan datos personales de otros colegas.

## Reglas de Negocio y Conexiones Externas
- **Regla de Privacidad Estadística (Instancia 2 §19.14):** Las comparativas se construyen exclusivamente sobre agregaciones estadísticas anonimizadas (promedios, medianas, top 5 histórico por escuela/departamento en `T_DETALLES_INSC_JD`), sin exponer nombres, apellidos ni números de CUIL de terceros.

## Criterios de Aceptación
1. **Debe** mostrar indicadores comparativos entre el puntaje del docente autenticado y el promedio histórico de la asignatura/departamento seleccionado.
2. **Debe** permitir filtrar la comparativa por Departamento provincial y por Asignatura/Espacio curricular.
3. **No debe** exponer en ningún gráfico, tabla o respuesta de API datos filiatorios (nombre, apellido o CUIL) de otros aspirantes al consultar estadísticas de competitividad.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar consultar la comparativa estadística para una materia y departamento específicos y verificar que se exhibe la relación entre el puntaje del docente y el promedio regional. *(Pasa)*
- **CP-02:** Probar inspeccionar la respuesta del endpoint estadístico y comprobar que contiene únicamente métricas agregadas (promedios, máximos, cantidades) y ningún dato personal de otros docentes. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consultas de agregación (`AVG`, `MAX`, `COUNT`) por departamento y materia sobre los datos históricos de LOM. |
| **Esfuerzo** | Medio | Componentes visuales de comparación estadística en el portal. |
| **Incertidumbre** | Baja | Basado en las mismas estadísticas agregadas de `T_DETALLES_INSC_JD` utilizadas por el Semáforo LOM. |

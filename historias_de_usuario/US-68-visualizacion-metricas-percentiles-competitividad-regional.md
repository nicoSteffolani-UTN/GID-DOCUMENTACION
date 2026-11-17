# [ID: US-68] Visualización de métricas y percentiles anonimizados de competitividad regional

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P3 (Media / Implementada)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar dentro de «Mis puntajes» indicadores estadísticos anonimizados y percentiles sobre cómo se ubica mi puntaje frente a los inscriptos de mi materia en el departamento,  
> **para** dimensionar mi posicionamiento relativo en la región sin que se expongan datos personales de otros colegas.

## Reglas de Negocio y Conexiones Externas
- **Integración en «Mis puntajes» (`ADR-037`):** No constituye una pantalla aislada con selectores ni altera los flujos de inscripción de Formulario C ni carga de legajo.
- **Distribución por Percentiles:** Grupo = materia/cargo × departamento. Calcula percentiles $P_{10}, P_{25}, P_{50}, P_{75}, P_{90}$ de la última convocatoria con datos en laboratorio (agregado en Oracle al arrancar).
- **Regla Estricta de Anonimato ($k \ge 10$):** Grupos de menos de 10 docentes no se muestran (`suficiente: false`).
- **LOM Recortada Escolar (`ADR-037 addendum`):** En «Listado de orden de mérito», expone la LOM pública de la escuela elegida con hasta 12 puestos (apellido, nombre, puntaje, referencia y alcance), emulando el sistema original de la Junta.

## Criterios de Aceptación
1. **Debe** integrarse directamente en la vista de «Mis puntajes» por cada materia o cargo inscripto en el Formulario C.
2. **Debe** calcular y exhibir la frase de posición relativa ("por encima de X de cada 10") y el histograma SVG con la distribución de percentiles del departamento.
3. **Debe** ocultar el gráfico y devolver `suficiente: false` para cualquier materia o grupo departamental con menos de 10 docentes inscriptos.
4. **Debe** permitir acceder a la LOM recortada de la escuela inscripta mostrando puestos públicos cercanos sin exponer nunca CUIL, DNI ni datos sensibles de terceros.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar consultar «Mis puntajes» para una asignatura inscripta con más de 10 docentes en el departamento y verificar la visualización del histograma de percentiles y la frase de posicionamiento relativo. *(Pasa)*
- **CP-02:** Probar consultar una materia con menos de 10 aspirantes en el departamento y comprobar que el sistema oculta el gráfico indicando que no hay suficientes inscriptos para resguardar el anonimato. *(Pasa)*
- **CP-03:** Probar consultar el listado recortado de la LOM de la escuela inscripta y verificar que lista los puestos públicos cercanos al docente sin exponer CUIL ni datos sensibles de terceros. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Consultas de agregación con `PERCENTILE_CONT` en Oracle MT4D, caché en memoria y LOM pública recortada. |
| **Esfuerzo** | Medio | Desplegable SVG en template Angular sin librerías externas pesadas y anonimato $k \ge 10$ (`ADR-037`). |
| **Incertidumbre** | Baja | Basado en las agregaciones de `T_DETALLES_INSC_JD` cacheadas al inicializar la API. |

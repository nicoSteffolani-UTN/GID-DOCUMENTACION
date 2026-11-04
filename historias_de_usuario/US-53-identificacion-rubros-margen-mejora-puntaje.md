# [ID: US-53] Identificación visual de rubros con mayor margen de mejora en el perfil de puntaje

- **Épica:** E10 - Simulador de Carrera Docente y Recomendador SIPEC
- **Prioridad:** P2 (Media)
- **Estimación:** 2 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** que el simulador y el recomendador resalten visualmente cuáles son los rubros del Decreto 257/19 donde tengo menor puntuación acumulada respecto del tope legal,  
> **para** saber en qué aspecto de mi legajo tengo margen real para sumar puntos y no realizar cursos en rubros que ya tengo al límite máximo.

## Reglas de Negocio y Conexiones Externas
- **Regla de Tope Reglamentario:** Si un docente ya alcanzó el tope máximo permitido por el Decreto 257/19 en un rubro determinado (por ejemplo, en capacitaciones o en cierto tipo de antecedentes), sumar más certificados de ese mismo rubro no incrementará su puntaje total, por lo que conviene orientar su esfuerzo a otros rubros con cupo disponible.

## Criterios de Aceptación
1. **Debe** mostrar barras de progreso por cada rubro del baremo indicando el porcentaje utilizado respecto del tope reglamentario (`Puntaje Actual / Tope del Rubro`).
2. **Debe** resaltar con un indicador de **«Oportunidad de mejora / Margen disponible»** aquellos rubros donde el docente se encuentra lejos del tope máximo.
3. **Debe** mostrar una etiqueta de **«Tope alcanzado»** cuando un rubro se encuentre al 100% de su límite legal, advirtiendo que nuevos antecedentes en esa misma categoría no sumarán puntos adicionales.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar visualizar el perfil de simulación de un docente con 0 puntos en Posgrados y bajo puntaje en Capacitaciones, verificando que ambos rubros se destacan como áreas con margen disponible para sumar puntos. *(Pasa)*
- **CP-02:** Probar con un rubro que alcanzó el 100% de su tope normativo y comprobar que se muestra el aviso de «Tope alcanzado». *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Cálculo porcentual sobre los topes definidos en `baremo-decreto-257.json`. |
| **Esfuerzo** | Bajo | Barras de progreso e indicadores visuales en los componentes de puntaje y simulador. |
| **Incertidumbre** | Nula | Datos ya presentes en el desglose de valoración. |

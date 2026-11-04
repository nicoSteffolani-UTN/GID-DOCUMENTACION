# [ID: US-50] Proyección de escuelas alcanzables al incrementar el puntaje en el Simulador de Carrera

- **Épica:** E10 - Simulador de Carrera Docente y Recomendador SIPEC
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que utiliza el simulador de carrera docente,  
> **quiero** visualizar a qué nuevas escuelas del padrón llegaría con semáforo competitivo (pasando de Rojo/Amarillo a Verde) gracias al puntaje simulado y cuántos puntos me faltan para alcanzar una escuela objetivo específica,  
> **para** traducir el aumento numérico de puntos en oportunidades reales de acceso a establecimientos concretos.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio (`ADR-024`):** Cruza el puntaje actual vs. el puntaje simulado contra el corte histórico de las 719+ escuelas en `T_DETALLES_INSC_JD`, mostrando las escuelas que se "desbloquean" o mejoran de categoría en el semáforo LOM.

## Criterios de Aceptación
1. **Debe** comparar el puntaje base actual y el nuevo puntaje simulado contra los promedios históricos LOM de las escuelas compatibles con el título del docente.
2. **Debe** listar y contabilizar las **«Nuevas escuelas a las que llegás»** (establecimientos donde el docente pasa de estar por debajo del promedio histórico a superarlo con el puntaje simulado).
3. **Debe** permitir seleccionar una **Escuela Objetivo** para visualizar la brecha exacta de puntos restantes y qué combinación de cursos/antigüedad permite alcanzar su promedio LOM.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar simular un incremento de +2,50 puntos en el simulador y verificar que el panel de impacto muestra el listado de escuelas que cambian de semáforo Amarillo/Rojo a Verde. *(Pasa)*
- **CP-02:** Probar elegir una escuela objetivo cuyo promedio histórico está 1,20 puntos por encima del puntaje actual del docente y comprobar que el simulador indica la brecha exacta y cuándo queda cubierta al agregar cursos al escenario. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Comparación diferencial de umbrales LOM antes y después de la simulación (ADR-024). |
| **Esfuerzo** | Medio | Integración entre el servicio de `simulador` y las estadísticas de `mapa` / LOM. |
| **Incertidumbre** | Baja | Implementado y documentado en ADR-024. |

# [ID: US-52] Recomendador de capacitaciones del catálogo tipo SIPEC ponderado según Decreto 257/19

- **Épica:** E10 - Simulador de Carrera Docente y Recomendador SIPEC
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** recibir recomendaciones personalizadas de cursos y capacitaciones de formación continua (catálogo tipo SIPEC / Red Provincial) que otorguen puntaje oficial según el Decreto 257/19,  
> **para** elegir propuestas formativas afines a mi área curricular que maximicen mi crecimiento en el orden de mérito.

## Reglas de Negocio y Conexiones Externas
- **Catálogo y Ponderación (`ADR-012`, `ADR-027`, `/capacitaciones`):** Se nutre del catálogo de cursos tipo SIPEC (`capacitaciones.mock.json`), filtrando capacitaciones con resolución ministerial y carga horaria **≥ 20 horas reloj**, calculando su aporte exacto (`horas × 0,017` puntos).

## Criterios de Aceptación
1. **Debe** listar en `/capacitaciones` (`GET /api/capacitaciones/recomendadas`) un catálogo de cursos y trayectos formativos indicando: nombre del curso, institución oferente, modalidad (virtual, presencial, semipresencial), área temática, carga horaria en horas reloj y **puntos que otorga según el Decreto 257/19**.
2. **Debe** priorizar y recomendar en primer lugar aquellas capacitaciones vinculadas al área curricular de los títulos del docente y que aún no figuren realizadas en su legajo.
3. **Debe** permitir filtrar el catálogo por área temática, modalidad y carga horaria.
4. **Debe** permitir enviar cualquier curso recomendado directamente al **Simulador de Puntaje** con un clic («Simular impacto de este curso») para ver cómo afectaría su posición en las escuelas.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `/capacitaciones` y verificar que cada curso exhibe su carga horaria y el puntaje exacto que suma según el Decreto 257/19 (ej. un curso de 40 h muestra `+0,68 pts`). *(Pasa)*
- **CP-02:** Probar filtrar las capacitaciones por modalidad «Virtual» y verificar que la lista se acota a los cursos virtuales disponibles. *(Pasa)*
- **CP-03:** Probar hacer clic en «Probar en el simulador» sobre un curso recomendado y comprobar que se abre el simulador con ese curso preseleccionado. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Filtrado por afinidad curricular, exclusión de cursos ya cargados en el legajo y cálculo de retorno en puntos. |
| **Esfuerzo** | Medio | Módulo backend `capacitaciones`, catálogo estructurado y vista de tarjetas con integración al simulador. |
| **Incertidumbre** | Baja | Basado en la estructura pública de ofertas de la Red Provincial / SIPEC (ADR-012). |

# [ID: US-39] Semáforo de competitividad LOM en el mapa (Comparación de puntaje propio vs. promedio histórico)

- **Épica:** E08 - Mapa Territorial Interactivo y Semáforo LOM
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar los marcadores de las escuelas en el mapa coloreados con un semáforo (Verde, Amarillo o Rojo) que compare mi puntaje contra el promedio histórico de la LOM de cada establecimiento,  
> **para** identificar de inmediato en qué escuelas mi puntaje resulta altamente competitivo, en cuáles compito parejo y en cuáles el corte histórico es muy superior.

## Reglas de Negocio y Conexiones Externas
- **Lógica del Semáforo LOM (`ADR-019`):**
  - Se calcula el puntaje de referencia de cada escuela/materia a partir de los promedios de los mejores puntajes (top 5 histórico) registrados en `T_DETALLES_INSC_JD` del laboratorio Oracle (719+ establecimientos con estadísticas).
  - **Verde (Favorable):** El puntaje del docente supera el umbral histórico de referencia de la escuela.
  - **Amarillo (Competitivo / Medio):** El puntaje del docente se encuentra próximo al promedio de referencia (dentro de la banda de competencia).
  - **Rojo (Exigente / Bajo margen):** El puntaje del docente se encuentra significativamente por debajo del promedio histórico de la escuela.
  - **Gris / Sin datos:** Establecimiento sin historial LOM registrado para el filtro actual.

## Criterios de Aceptación
1. **Debe** colorear cada marcador de escuela en el mapa según la relación entre el puntaje del docente autenticado y el promedio histórico LOM del establecimiento (`Verde`, `Amarillo`, `Rojo` o `Gris`).
2. **Debe** mostrar dentro del detalle de la escuela el valor numérico del **promedio histórico LOM** de ese establecimiento junto al **puntaje del docente** y la diferencia en puntos (`+X,XX pts` o `-X,XX pts`).
3. **Debe** actualizar dinámicamente los colores del semáforo cuando el docente filtra el mapa por una asignatura específica en la que su título tiene distinto alcance.
4. **Debe** incluir una leyenda explicativa permanente en el mapa detallando el significado de cada color del semáforo.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar visualizar el mapa con un docente con puntaje cargado y verificar que las escuelas muestran su color de semáforo (`Verde`, `Amarillo`, `Rojo`) acorde a la comparativa contra `T_DETALLES_INSC_JD`. *(Pasa)*
- **CP-02:** Probar abrir el detalle de una escuela en color `Verde` y comprobar que el puntaje del docente es superior al promedio LOM informado en la tarjeta. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Agregación estadística de `T_DETALLES_INSC_JD` en Oracle Lab y cruce dinámico con el puntaje del usuario (ADR-019). |
| **Esfuerzo** | Medio | Endpoint de métricas LOM por establecimiento y renderizado de marcadores semaforizados en Leaflet. |
| **Incertidumbre** | Baja | Conectado y verificado contra las 719 escuelas con LOM en el laboratorio Oracle. |

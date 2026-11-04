# [ID: US-34] Revisión inteligente previa a la confirmación del Formulario C (Alertas de riesgo y omisiones)

- **Épica:** E07 - Ayudas a la Decisión y Recomendación de Postulación
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que está por confirmar su Formulario C,  
> **quiero** que el sistema ejecute un diagnóstico automático sobre mi selección y me advierta si estoy dejando cupos vacíos, si elegí solo escuelas de muy difícil acceso por puntaje o si tengo antecedentes sin validar,  
> **para** corregir posibles errores estratégicos u omisiones antes de cerrar definitivamente mi inscripción.

## Reglas de Negocio y Conexiones Externas
- **Reglas de Revisión Inteligente (ADR-025):**
  1. Alerta por subutilización de cupo (ej. menos de 3 escuelas o menos de 5 materias disponibles cuando existen más asignaturas con alcance en esa escuela).
  2. Alerta por concentración de riesgo (ej. las 3 escuelas elegidas tienen promedio LOM muy superior al puntaje del docente — todas en rojo).
  3. Alerta de materias sin alcance de título (vinculado a US-25).
  4. Alerta de antecedentes en estado `Pendiente` o `Rechazado` en el legajo.

## Criterios de Aceptación
1. **Debe** analizar la configuración actual del Formulario C del docente y mostrar un panel de **«Diagnóstico y Revisión de tu Inscripción»**.
2. **Debe** advertir cuando el docente haya seleccionado menos de 3 escuelas o esté desaprovechando materias con alcance `Docente` dentro de las escuelas elegidas.
3. **Debe** alertar cuando todas las escuelas seleccionadas presenten una brecha desfavorable de puntaje (alta probabilidad de quedar lejos en el orden de mérito).
4. **Debe** presentar estas observaciones como recomendaciones no bloqueantes (salvo los topes reglamentarios obligatorios), permitiendo al docente ajustar su formulario o confirmar su decisión.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar armar un Formulario C con solo 1 escuela y 1 materia teniendo más alcances disponibles, y verificar que el revisor inteligente sugiere aprovechar los cupos restantes. *(Pasa)*
- **CP-02:** Probar elegir 3 escuelas donde el puntaje histórico supera ampliamente al del docente y comprobar que el sistema emite la advertencia de alto riesgo competitivo sugiriendo incluir al menos una «Escuela Segura». *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Reglas heurísticas de evaluación sobre el borrador del Formulario C, el legajo y el mapa LOM. |
| **Esfuerzo** | Medio | Servicio analizador de formulario y componente de checklist/alertas previas a la confirmación. |
| **Incertidumbre** | Nula | Reglas de negocio de ayuda a la decisión definidas en ADR-025. |

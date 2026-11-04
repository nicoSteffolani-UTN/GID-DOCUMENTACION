# [ID: US-36] Recomendación de vacantes activas que más convienen según puntaje, alcance y cercanía

- **Épica:** E07 - Ayudas a la Decisión y Recomendación de Postulación
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** consultar un listado priorizado de las vacantes publicadas que mejor se ajustan a mi perfil (por tener alcance de título, puntaje competitivo en esa escuela y cercanía geográfica),  
> **para** identificar rápidamente las oportunidades de cobertura donde tengo mayores chances reales de designación.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio (ADR-025):** Filtra y ordena las vacantes vigentes (`VT_PUBLI_EMPRE_MAT_ASPIRANTES` / publicaciones de la Junta) priorizando aquellas donde el docente posee alcance habilitante, semáforo LOM favorable y distancia dentro de su radio de viaje.

## Criterios de Aceptación
1. **Debe** cruzar las vacantes activas publicadas con los títulos del legajo del docente para mostrar en primer término aquellas asignaturas que está habilitado a dictar.
2. **Debe** ordenar o destacar las vacantes según el nivel de conveniencia: posición del puntaje del docente frente al promedio LOM de la escuela y pertenencia a las escuelas de su Formulario C o lista de interés.
3. **Debe** indicar en cada tarjeta de vacante sugerida el establecimiento, la asignatura, la carga horaria, el turno, el color de semáforo LOM para el docente y la distancia aproximada en kilómetros si tiene ubicación configurada.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar consultar las vacantes recomendadas con un docente de Matemática y verificar que se priorizan las vacantes de su incumbencia ordenadas por mejor relación de puntaje/semáforo. *(Pasa)*
- **CP-02:** Probar verificar que cada vacante sugerida explica por qué se recomienda (ej. «Tienes alcance Docente y tu puntaje supera el promedio histórico de esta escuela»). *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Ranking ponderado entre catálogo de vacantes, alcance de título, semáforo LOM y distancia. |
| **Esfuerzo** | Medio | Endpoint de vacantes convenientes y vista de tarjetas recomendadas en el portal. |
| **Incertidumbre** | Baja | Fuentes de datos ya integradas en el backend. |

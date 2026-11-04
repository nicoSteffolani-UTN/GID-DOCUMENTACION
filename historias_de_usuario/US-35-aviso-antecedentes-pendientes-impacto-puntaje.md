# [ID: US-35] Aviso de antecedentes pendientes de validación con impacto potencial en el puntaje de inscripción

- **Épica:** E07 - Ayudas a la Decisión y Recomendación de Postulación
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar durante la preparación de mi inscripción un aviso que me indique cuántos puntos adicionales están retenidos en antecedentes en estado «Pendiente» y cómo cambiaría mi posición en las escuelas elegidas una vez validados,  
> **para** dimensionar el impacto de mi documentación en trámite sobre mi competitividad en la LOM.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio (ADR-025):** Conecta el estado del legajo (`Pendiente` vs `Validado`) con la simulación de competitividad en las escuelas del Formulario C.

## Criterios de Aceptación
1. **Debe** detectar si el docente posee títulos, cursos u otros antecedentes en estado `Pendiente` dentro de su legajo.
2. **Debe** calcular cuántos puntos adicionales según el Decreto 257/19 aportarían esos antecedentes si fueran validados por la Junta.
3. **Debe** mostrar en el resumen de ayuda a la decisión la comparación entre su puntaje validado actual y su puntaje potencial con pendientes aprobados, indicando si alguna escuela de su Formulario C pasaría de semáforo Amarillo/Rojo a Verde.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar con un docente que tiene 2 cursos en estado `Pendiente` en su legajo y verificar que el módulo de ayuda informa la cantidad de antecedentes pendientes y el incremento estimado de puntaje que representan. *(Pasa)*
- **CP-02:** Probar con un docente que tiene el 100% de sus antecedentes `Validados` y comprobar que el indicador muestra que todo su puntaje cargado ya se encuentra computado. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Diferencia aritmética entre cálculo de baremo sobre antecedentes `Validados` vs. `Validados + Pendientes`. |
| **Esfuerzo** | Bajo | Indicador comparativo en las vistas de puntaje y ayuda del Formulario C. |
| **Incertidumbre** | Nula | Usa el mismo motor de valoración reglamentaria de E04. |

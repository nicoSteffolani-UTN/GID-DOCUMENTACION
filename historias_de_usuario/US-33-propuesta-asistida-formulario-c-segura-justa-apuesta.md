# [ID: US-33] Propuesta asistida de Formulario C con clasificación estratégica de escuelas (Segura, Justa y Apuesta)

- **Épica:** E07 - Ayudas a la Decisión y Recomendación de Postulación
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que debe elegir sus 3 establecimientos en el Formulario C,  
> **quiero** que el sistema me sugiera una combinación equilibrada de escuelas clasificándolas en «Escuela Segura», «Escuela Justa» y «Escuela Apuesta» según mi puntaje frente al histórico de corte LOM,  
> **para** optimizar mis probabilidades reales de acceder a un cargo sin desperdiciar mis tres cupos anuales a ciegas.

## Reglas de Negocio y Conexiones Externas
- **Lógica de Ayuda a la Decisión (ADR-025):**
  - **Escuela Segura (Verde):** Establecimiento donde el puntaje del aspirante supera con holgura el promedio del top 5 histórico de la LOM (`T_DETALLES_INSC_JD`).
  - **Escuela Justa / Competitiva (Amarillo):** Establecimiento donde el puntaje del aspirante se encuentra dentro del rango cercano al corte histórico.
  - **Escuela Apuesta / Aspiracional (Rojo moderado o alta demanda):** Establecimiento deseado de mayor exigencia de puntaje o con vacantes atractivas donde el docente compite como opción aspiracional.
- **Conexión de Datos:** Cruce entre el puntaje del docente (oficial o estimado Dto. 257/19), los alcances de su título, su ubicación/radio de viaje y las estadísticas históricas de LOM en Oracle Lab.

## Criterios de Aceptación
1. **Debe** ofrecer en `/formulario-c` una herramienta de **«Propuesta sugerida de escuelas»** que recomiende hasta 3 establecimientos compatibles con el título del docente.
2. **Debe** clasificar y etiquetar cada escuela sugerida dentro de las tres categorías estratégicas: **«Segura»**, **«Justa»** o **«Apuesta»**, explicando brevemente el motivo (diferencia en puntos entre el puntaje del docente y el promedio histórico de la escuela).
3. **Debe** permitir al docente aplicar una o todas las escuelas sugeridas directamente a su borrador del Formulario C con un clic, manteniendo siempre la libertad de modificarlas manualmente.
4. **Debe** filtrar las sugerencias únicamente entre establecimientos que dicten asignaturas para las cuales el título del docente posea alcance habilitante.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar solicitar la propuesta asistida de Formulario C con un docente con título y puntaje cargado y verificar que el sistema devuelve sugerencias clasificadas en Escuela Segura, Justa y Apuesta con su comparativa de puntaje. *(Pasa)*
- **CP-02:** Probar hacer clic en «Agregar al Formulario C» sobre una escuela sugerida y verificar que se incorpora al borrador junto con las materias recomendadas. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Algoritmo multicriterio que combina alcance de título, puntaje propio, promedios históricos LOM y distancia geográfica (ADR-025). |
| **Esfuerzo** | Medio | Motor de recomendación en backend y tarjetas de sugerencia interactivas en el Formulario C. |
| **Incertidumbre** | Baja | Basado en la distribución histórica de `T_DETALLES_INSC_JD` ya relevada en el laboratorio. |

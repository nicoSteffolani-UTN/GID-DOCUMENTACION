# [ID: US-42] Visualización de vacantes publicadas por la Junta dentro de la ficha de escuela en el mapa

- **Épica:** E08 - Mapa Territorial Interactivo y Semáforo LOM
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** consultar directamente dentro de la ficha de cada escuela en el mapa las vacantes de interinatos y suplencias publicadas por la Junta de Clasificación para ese establecimiento,  
> **para** conocer qué materias, turnos, cargas horarias y fechas de cierre de cobertura se encuentran vigentes en ese lugar.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Vista de publicaciones de vacantes de la Junta Docente en el laboratorio Oracle (`VT_PUBLI_EMPRE_MAT_ASPIRANTES`, ADR-016 / actualización de septiembre).

## Criterios de Aceptación
1. **Debe** indicar en el marcador o resumen de cada escuela la cantidad de vacantes activas publicadas por la Junta de Clasificación.
2. **Debe** detallar dentro del panel de la escuela seleccionada cada vacante publicada incluyendo: asignatura/cargo, carácter (Interinato / Suplencia), turno, cantidad de horas cátedra o módulos y estado/cierre de la publicación.
3. **Debe** resaltar aquellas vacantes de la escuela para las cuales el título del docente autenticado posee alcance habilitante.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar en el mapa una escuela con vacantes activas y verificar que se despliega el listado de materias vacantes con su carácter, horas y turno provenientes de las publicaciones de la Junta. *(Pasa)*
- **CP-02:** Probar seleccionar una escuela sin vacantes vigentes y comprobar que se informa «Sin vacantes publicadas actualmente» manteniendo visible su información de Semáforo LOM. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Cruce por código de empresa/establecimiento entre el padrón geográfico y la vista `VT_PUBLI_EMPRE_MAT_ASPIRANTES` en Oracle. |
| **Esfuerzo** | Medio | Adaptador de lectura en backend y sección de vacantes en el panel de detalle del mapa. |
| **Incertidumbre** | Baja | Vista de Oracle Lab verificada e integrada en `ADR-016`. |

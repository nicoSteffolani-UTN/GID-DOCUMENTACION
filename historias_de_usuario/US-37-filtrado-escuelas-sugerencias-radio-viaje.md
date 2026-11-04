# [ID: US-37] Filtrado de escuelas y sugerencias por radio de viaje en kilómetros

- **Épica:** E07 - Ayudas a la Decisión y Recomendación de Postulación
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** filtrar las escuelas recomendadas del Formulario C y del mapa según un radio máximo de distancia en kilómetros desde mi ubicación de referencia,  
> **para** descartar establecimientos cuyo traslado diario me resulte inviable y enfocar mi decisión en escuelas accesibles.

## Reglas de Negocio y Conexiones Externas
- **Regla Técnica (ADR-025):** Cálculo de distancia geodésica (fórmula de Haversine) entre el punto de referencia del docente y las coordenadas geográficas (`latitud`, `longitud`) de los establecimientos del padrón en Oracle Lab.

## Criterios de Aceptación
1. **Debe** permitir activar y ajustar un filtro de **«Radio de viaje (km)»** (ej. 5 km, 10 km, 20 km, 50 km o Toda la provincia) tanto en las sugerencias del Formulario C como en el explorador territorial.
2. **Debe** calcular la distancia en kilómetros entre la ubicación de referencia del docente y cada establecimiento georreferenciado del padrón.
3. **Debe** mostrar la distancia estimada en kilómetros (`X,X km`) en la ficha de cada escuela sugerida.
4. **Debe** excluir de las sugerencias automáticas aquellas escuelas que superen el radio máximo seleccionado por el docente.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar un radio de viaje de 10 km desde Córdoba Capital y verificar que solo se sugieren establecimientos ubicados dentro de ese radio de distancia. *(Pasa)*
- **CP-02:** Probar ampliar el radio a 50 km y comprobar que se incorporan establecimientos del Gran Córdoba / departamentos aledaños mostrando su distancia en kilómetros. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Cálculo matemático de distancia por coordenadas geográficas y filtrado en memoria/consulta. |
| **Esfuerzo** | Bajo | Control deslizante/selector de radio en UI y función utilitaria de distancia en backend/frontend. |
| **Incertidumbre** | Nula | Coordenadas presentes en el padrón de establecimientos de mapa. |

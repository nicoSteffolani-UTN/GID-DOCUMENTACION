# [ID: US-38] Visualización en mapa interactivo de establecimientos educativos y límites departamentales

- **Épica:** E08 - Mapa Territorial Interactivo y Semáforo LOM
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** explorar en un mapa interactivo de la provincia de Córdoba la ubicación geográfica de las escuelas secundarias estatales junto con los límites departamentales,  
> **para** analizar visualmente la distribución territorial de los establecimientos antes de definir mi inscripción anual o mis preferencias de vacantes.

## Reglas de Negocio y Conexiones Externas
- **Tecnología y Cartografía:** Librería Leaflet (`ADR-008`) con polígonos departamentales GeoJSON (`departamentos-cordoba.geojson`) y padrón georreferenciado de establecimientos desde el laboratorio Oracle (`ADR-016`, `GET /api/mapa/escuelas`).

## Criterios de Aceptación
1. **Debe** renderizar en la ruta `/mapa` un mapa interactivo de la provincia de Córdoba con controles de zoom, desplazamiento y capa de polígonos de los 26 departamentos provinciales.
2. **Debe** posicionar marcadores georreferenciados por cada establecimiento educativo secundario estatal que posea coordenadas válidas (`latitud`, `longitud`).
3. **Debe** desplegar una tarjeta emergente (*popup* o panel lateral) al hacer clic sobre el marcador de una escuela, mostrando: denominación oficial, código de establecimiento, departamento, localidad, domicilio y resumen de vacantes/LOM.
4. **Debe** garantizar tiempos de carga y respuesta visual fluidos al navegar o hacer zoom sobre zonas de alta densidad de escuelas (como Córdoba Capital y Gran Córdoba).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `/mapa` y verificar que se dibuja el mapa de Córdoba con la capa departamental y los marcadores de escuelas secundarias. *(Pasa)*
- **CP-02:** Probar hacer clic sobre el marcador de una escuela en el mapa y comprobar que se abre su ficha con nombre, ubicación, promedio histórico LOM y vacantes. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Integración de Leaflet en Angular (compatible con SSR), capas GeoJSON y manejo de cientos de marcadores. |
| **Esfuerzo** | Alto | Componente de mapa, popups enriquecidos y endpoint de establecimientos georreferenciados. |
| **Incertidumbre** | Baja | Spike técnico validado en PD-21 y ADR-008 / ADR-016. |

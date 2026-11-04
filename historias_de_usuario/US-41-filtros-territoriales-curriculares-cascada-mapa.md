# [ID: US-41] Filtros territoriales y curriculares en cascada sobre el mapa (Departamento, Localidad, Barrio, Materia y Semáforo)

- **Épica:** E08 - Mapa Territorial Interactivo y Semáforo LOM
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** filtrar las escuelas del mapa combinando criterios en cascada (Departamento, Localidad, Barrio, Asignatura, disponibilidad de vacantes y color de Semáforo LOM),  
> **para** acotar rápidamente la vista a los establecimientos que me interesan y centrar el mapa en la región seleccionada.

## Reglas de Negocio y Conexiones Externas
- **Comportamiento de Cascada:** Al elegir un Departamento se actualizan las Localidades pertenecientes a ese departamento y el mapa hace zoom (*fitBounds*) sobre el polígono departamental seleccionado.

## Criterios de Aceptación
1. **Debe** proveer una barra de filtros en `/mapa` con selectores para: **Departamento**, **Localidad**, **Barrio/Zona**, **Asignatura/Materia**, **Color de Semáforo LOM** (`Todos`, `Verde`, `Amarillo`, `Rojo`) y **Solo con vacantes publicadas**.
2. **Debe** encadenar en cascada los selectores geográficos (seleccionar un Departamento filtra las Localidades disponibles).
3. **Debe** centrar y ajustar automáticamente el zoom del mapa sobre el departamento o conjunto de escuelas resultantes al aplicar un filtro territorial.
4. **Debe** mostrar el contador de escuelas visibles que cumplen con los filtros activos y ofrecer un botón de **«Limpiar filtros»** para restablecer la vista provincial completa.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar el departamento «CAPITAL» y el filtro de semáforo «Verde», verificando que el mapa hace zoom sobre Capital y muestra únicamente las escuelas con semáforo verde. *(Pasa)*
- **CP-02:** Probar activar el interruptor «Solo escuelas con vacantes» y comprobar que se ocultan los establecimientos sin vacantes activas. *(Pasa)*
- **CP-03:** Probar presionar «Limpiar filtros» y verificar que se restablece la totalidad de marcadores y el encuadre general de la provincia. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Filtrado reactivo combinatorio sobre marcadores de Leaflet y ajuste automático de *bounding box*. |
| **Esfuerzo** | Medio | Barra de filtros en cascada en Angular y sincronización con la capa de marcadores y listado lateral. |
| **Incertidumbre** | Nula | Implementado y verificado en las pruebas del módulo `/mapa`. |

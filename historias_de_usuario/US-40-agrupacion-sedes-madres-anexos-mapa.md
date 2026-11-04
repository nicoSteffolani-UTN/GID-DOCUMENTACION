# [ID: US-40] Agrupación jerárquica de Sedes Madres con sus Anexos y Extensiones Áulicas en el mapa

- **Épica:** E08 - Mapa Territorial Interactivo y Semáforo LOM
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que explora el mapa territorial,  
> **quiero** visualizar la relación entre cada escuela Sede Madre y sus respectivos Anexos o Extensiones Áulicas rurales/urbanas,  
> **para** comprender si una vacante o inscripción corresponde al edificio central o a un anexo dependiente y conocer la ubicación de ambos.

## Reglas de Negocio y Conexiones Externas
- **Regla de Dominio:** En el sistema educativo provincial muchos IPEM/IPEA/IPET poseen Anexos o Extensiones Áulicas que dependen administrativamente de una Sede Madre pero pueden estar ubicados en otra localidad o paraje.

## Criterios de Aceptación
1. **Debe** identificar en el modelo de datos del mapa qué establecimientos son **Sede Madre / Central** y cuáles son **Anexos** vinculados a una sede principal.
2. **Debe** mostrar en la ficha de una Sede Madre el listado de sus Anexos dependientes (y viceversa, indicar en el Anexo cuál es su Sede Madre).
3. **Debe** permitir distinguir visualmente en el mapa o mediante filtro las sedes principales de sus anexos asociados.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar seleccionar en el mapa un establecimiento que posea anexos asociados y verificar que en su tarjeta informativa se listan los anexos vinculados. *(Pasa)*
- **CP-02:** Probar seleccionar un Anexo en el mapa y comprobar que informa claramente a qué Sede Madre pertenece. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Relación jerárquica padre–hijo entre códigos de empresa/CUE en el padrón de establecimientos. |
| **Esfuerzo** | Medio | Normalización en el adaptador de mapa y visualización de vínculos sede–anexo en la tarjeta del establecimiento. |
| **Incertidumbre** | Baja | Estructura contemplada en la documentación de arquitectura del módulo de mapa. |

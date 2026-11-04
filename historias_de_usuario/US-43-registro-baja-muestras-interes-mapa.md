# [ID: US-43] Registro y baja de muestras de interés sobre escuelas y vacantes desde el mapa

- **Épica:** E08 - Mapa Territorial Interactivo y Semáforo LOM
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** marcar o desmarcar escuelas y vacantes como «De mi interés» directamente desde el mapa territorial,  
> **para** armar una lista personal de seguimiento de establecimientos candidatos antes de decidir mis 3 escuelas del Formulario C o recibir avisos focalizados.

## Reglas de Negocio y Conexiones Externas
- **Alcance Funcional (`US-10 lite` en Trazabilidad):** El registro de interés es una herramienta personal de seguimiento y preselección del docente (`POST /api/mapa/intereses` y `DELETE /api/mapa/intereses/:id`); no constituye una postulación formal ni reemplaza al Formulario C.
- **Persistencia:** Base de datos PostgreSQL del portal (`interes_vacante`, ADR-028).

## Criterios de Aceptación
1. **Debe** ofrecer un botón de **«Me interesa / Guardar en mis intereses»** en la ficha de escuela/vacante del mapa (`POST /api/mapa/intereses`).
2. **Debe** permitir quitar un establecimiento o vacante de la lista de intereses en cualquier momento (`DELETE /api/mapa/intereses/:id`).
3. **Debe** persistir los intereses registrados por CUIL en la base de datos del portal para que se mantengan disponibles entre sesiones y puedan consultarse desde la lista de intereses o pre-cargarse como referencia al armar el Formulario C.
4. **Debe** aclarar en la interfaz que marcar interés es una acción de seguimiento personal y no implica inscripción oficial.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar marcar una escuela/vacante como «Me interesa» desde el mapa y verificar que cambia su estado a guardado y aparece en el listado de intereses del docente (`GET /api/mapa/intereses`). *(Pasa)*
- **CP-02:** Probar quitar el interés marcado previamente y comprobar que se elimina de la lista de intereses del usuario. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | CRUD de marcadores de interés asociados al CUIL del docente en PostgreSQL. |
| **Esfuerzo** | Bajo | Endpoints REST en `/api/mapa/intereses` y botón toggle en la tarjeta del mapa. |
| **Incertidumbre** | Nula | Implementado y cubierto por pruebas de integración en el repositorio. |

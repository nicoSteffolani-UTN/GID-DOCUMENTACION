# [ID: US-06] Panel de inicio con estado actual del ciclo administrativo docente

- **Épica:** E02 - Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que ingresa al portal,  
> **quiero** visualizar en la pantalla de inicio un resumen claro de la etapa vigente del calendario anual y el estado de avance de mis trámites personales,  
> **para** saber de inmediato qué acciones tengo habilitadas en este momento (carga de legajo, espera de evaluación, análisis con puntaje o inscripción en Formulario C).

## Reglas de Negocio y Conexiones Externas
- **Proceso de Negocio:** Ciclo administrativo anual de Junta Secundaria dividido en fases secuenciales (Carga de Legajo → Evaluación Interanual de Junta sin inscripción → Apoyo a la Decisión con Puntaje → Convocatoria Formulario C → Publicación LOM).

## Criterios de Aceptación
1. **Debe** presentar en la ruta `/home` una guía visual paso a paso con las etapas principales del circuito del aspirante (Legajo, Formulario C, Puntaje, Mapa y Herramientas de Decisión).
2. **Debe** indicar el estado actual de la convocatoria anual (ej. inscripción abierta o período interanual de evaluación de antecedentes).
3. **Debe** mostrar indicadores resumidos del propio docente: cantidad de antecedentes cargados/validados en su legajo, estado de su Formulario C en el ciclo actual (borrador / confirmado / sin iniciar) y su puntaje oficial o estimado disponible.
4. **Debe** ofrecer accesos directos desde cada tarjeta o paso del panel hacia el módulo correspondiente (`/legajo`, `/formulario-c`, `/puntaje`, `/mapa`, `/simulador`).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `/home` con un docente con legajo y Formulario C cargados y verificar que el panel refleja sus indicadores actualizados y enlaces activos. *(Pasa)*
- **CP-02:** Probar ingresar a `/home` cuando la ventana de inscripción del Formulario C se encuentra cerrada y verificar que el indicador informa el estado del período vigente. *(Pasa)*
- **CP-03:** Probar hacer clic en cada tarjeta de acción rápida del panel de inicio y comprobar la navegación directa hacia la pantalla correspondiente. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consolidación de estados de resumen del usuario autenticado. |
| **Esfuerzo** | Bajo | Maquetación del dashboard inicial y consumo de estados resumidos de la API. |
| **Incertidumbre** | Nula | Flujo de navegación definido en el diseño del MVP. |

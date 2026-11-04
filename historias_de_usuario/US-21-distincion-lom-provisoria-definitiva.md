# [ID: US-21] Distinción de estado de publicación de LOM (Provisoria vs. Definitiva) y ventana de exhibición

- **Épica:** E04 - Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar claramente si el puntaje y orden de mérito exhibidos corresponden a la publicación de LOM Provisoria (con período de reclamos abierto) o a la LOM Definitiva del ciclo lectivo,  
> **para** saber si mi calificación ya se encuentra firme o si estoy dentro del plazo reglamentario para solicitar una revisión ante la Junta.

## Reglas de Negocio y Conexiones Externas
- **Proceso de Negocio:** En el calendario anual de la Junta de Clasificación Secundaria, tras el cierre de la inscripción se publica primero una **LOM Provisoria** (habilitando un período de 10 días hábiles para reclamos) y, una vez resueltos los reclamos por los vocales, se publica la **LOM Definitiva** que rige la cobertura de vacantes.

## Criterios de Aceptación
1. **Debe** mostrar en la cabecera de la sección `/puntaje` una etiqueta de estado que identifique la instancia de la nómina: `Valoración en curso / Estimada`, `LOM Provisoria Publicada` o `LOM Definitiva Vigente`.
2. **Debe** informar las fechas de vigencia del período de exhibición y reclamos cuando el estado sea `LOM Provisoria Publicada`.
3. **Debe** habilitar el acceso directo para iniciar un reclamo de revisión únicamente cuando la publicación se encuentre en instancia provisoria o dentro de la ventana reglamentaria habilitada.
4. **Debe** indicar el carácter firme e inapelable para coberturas del ciclo vigente cuando el estado sea `LOM Definitiva Vigente`.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar visualizar `/puntaje` durante el estado de LOM Provisoria y comprobar que se exhibe el indicador de instancia provisoria y el plazo de revisión. *(Pasa)*
- **CP-02:** Probar visualizar `/puntaje` en instancia de LOM Definitiva y verificar que se muestra el sello de puntaje definitivo del ciclo lectivo. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Parametrización del estado del ciclo de publicación en la respuesta del servicio de puntaje. |
| **Esfuerzo** | Bajo | Indicadores de estado e información contextual en la interfaz de `/puntaje`. |
| **Incertidumbre** | Nula | Etapas del proceso administrativo relevadas en la documentación de proceso (Instancia 5 §1.2). |

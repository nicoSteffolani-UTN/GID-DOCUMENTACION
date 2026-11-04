# [ID: US-65] Presentación digital de reclamos de revisión de LOM o antecedentes por el aspirante

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** presentar un reclamo formal en línea durante el período reglamentario de exhibición de la LOM Provisoria adjuntando mi descargo y documentación respaldatoria si detecto una diferencia en mi puntaje o en la valoración de un antecedente,  
> **para** solicitar la revisión por parte de la Junta de Clasificación sin tener que concurrir presencialmente.

## Reglas de Negocio y Conexiones Externas
- **Plazo Legal:** Solo se admite la apertura de nuevos reclamos dentro de la ventana reglamentaria de exhibición de LOM Provisoria (10 días hábiles posteriores a la publicación provisoria).

## Criterios de Aceptación
1. **Debe** permitir iniciar un reclamo vinculándolo al rubro de puntaje o al antecedente específico observado mientras el período de reclamos se encuentre abierto.
2. **Debe** exigir una descripción fundamentada del motivo del reclamo (mínimo 20 caracteres) y permitir adjuntar opcionalmente un documento PDF respaldatorio (máximo 1 MB).
3. **Debe** asignar un número único de ticket/reclamo con estado inicial `En Revisión` y fecha/hora oficial de presentación.
4. **Debe** impedir la presentación de nuevos reclamos una vez cerrada la ventana de exhibición de la LOM Provisoria.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar registrar un reclamo durante la ventana habilitada detallando el motivo y verificar que se genera el número de ticket en estado `En Revisión`. *(Pasa)*
- **CP-02:** Probar intentar presentar un reclamo fuera del período reglamentario de exhibición y verificar que el sistema bloquea la acción informando el cierre del plazo. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Gestión de ciclo de vida de reclamos y control de ventana temporal. |
| **Esfuerzo** | Medio | Formulario de apertura de reclamo y persistencia asociada al legajo/inscripción. |
| **Incertidumbre** | Baja | Proceso relevado en el circuito administrativo anual (Instancia 5 §1.2). |

# [ID: US-02] Verificación obligatoria de Identidad CiDi Nivel 2

- **Épica:** E01 - Identidad Digital, Autenticación y Sesión (CiDi)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** autoridad de aplicación del sistema,  
> **quiero** exigir que toda cuenta que acceda al portal cuente con Nivel 2 de verificación de identidad en Ciudadano Digital,  
> **para** asegurar que las operaciones sobre legajos e inscripciones sean realizadas exclusivamente por ciudadanos con identidad fehacientemente comprobada.

## Reglas de Negocio y Conexiones Externas
- **Normativa:** Requisito institucional del Ministerio de Educación de Córdoba para trámites con validez administrativa (CiDi Nivel 2 verificado).
- **Integración Externa:** Atributo de nivel de seguridad devuelto por el proveedor de identidad CiDi (ADR-031).

## Criterios de Aceptación
1. **Debe** verificar en el backend que el atributo de nivel de seguridad informado por CiDi sea igual o superior a **Nivel 2** antes de emitir el `JWT` operativo del portal.
2. **Debe** permitir el acceso normal a todas las funcionalidades del rol asignado cuando el usuario posea CiDi Nivel 2.
3. **Debe** impedir la emisión de sesión operativa y bloquear preventivamente el ingreso si el usuario posee únicamente **CiDi Nivel 1** (identidad no verificada).
4. **Debe** presentar una pantalla informativa clara cuando se detecte Nivel 1, explicando el requisito normativo e indicando los pasos oficiales en la app o centro de constatación de CiDi para elevar la cuenta a Nivel 2.
5. **Debe** impedir el consumo de cualquier endpoint de negocio (`/api/legajo`, `/api/formulario-c`, `/api/junta`, etc.) a cuentas que no cumplan con el Nivel 2.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar el ingreso con un ciudadano que posee cuenta CiDi Nivel 2 y verificar que accede sin restricciones a su panel principal. *(Pasa)*
- **CP-02:** Probar el ingreso con el usuario de prueba de CiDi Nivel 1 (CUIL `20987654321`) y verificar que el sistema bloquea la entrada mostrando el aviso instructivo de subsanación de nivel. *(Falla controlada)*
- **CP-03:** Probar invocar directamente la API de negocio con credenciales de un usuario Nivel 1 y verificar la respuesta HTTP 403 (Prohibido). *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Validación condicional de atributo de seguridad en el servicio de autenticación. |
| **Esfuerzo** | Bajo | Regla en backend y vista informativa de bloqueo por nivel insuficiente en Angular. |
| **Incertidumbre** | Nula | Regla de negocio taxativa y definida desde el inicio del relevamiento. |

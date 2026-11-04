# [ID: US-74] Control de acceso basado en roles (RBAC) y protección de cabeceras y secretos

- **Épica:** E14 - Gobernanza, Seguridad y Auditoría Inmutable
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Sistema / Autoridad de Seguridad

## Descripción (Narrativa)
> **Como** responsable de arquitectura y seguridad del portal,  
> **quiero** que todos los endpoints de la API REST y rutas del cliente apliquen autorización estricta basada en roles (RBAC), validación de pertenencia por CUIL, cabeceras HTTP seguras y gestión externalizada de secretos,  
> **para** cumplir con las directrices de seguridad informática del Gobierno de la Provincia de Córdoba.

## Reglas de Negocio y Conexiones Externas
- **Estándares de Seguridad (`ADR-005`, `ADR-006`, `ADR-014`, `SCM.md`):**
  1. Verificación de firma `JWT` y rol en cada endpoint protegido (`authMiddleware` + `requireRole`).
  2. Prevención de acceso horizontal (*IDOR*): un aspirante solo opera sobre recursos asociados al CUIL de su propio token `JWT`.
  3. Cero credenciales en código fuente (`.env` excluido por `.gitignore`, uso exclusivo de variables de entorno).

## Criterios de Aceptación
1. **Debe** validar en el backend la firma criptográfica, vigencia y *claims* (`cuil`, `rol`, `nivelCidi`) del token `JWT` en cada petición a rutas protegidas de `/api/*`.
2. **Debe** extraer la identidad del docente aspirante directamente del token `JWT` verificado en el servidor para las operaciones sobre su legajo, puntaje y Formulario C, impidiendo que un usuario suplante el CUIL de otro mediante parámetros de URL o body.
3. **Debe** aplicar cabeceras de seguridad HTTP (`Helmet` / políticas CORS restrictivas) y límite de tamaño de payload en el servidor Express.
4. **No debe** versionar contraseñas, claves privadas ni cadenas de conexión sensibles en el repositorio Git, manteniendo únicamente plantillas `.env.example` con valores ficticios.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar enviar una petición a `/api/legajo` intentando inyectar en el body el CUIL de otro docente y verificar que el backend opera exclusivamente sobre el CUIL del token autenticado o rechaza la manipulación. *(Pasa)*
- **CP-02:** Probar inspeccionar el historial y árbol del repositorio verificando que no existen archivos `.env` ni claves reales expuestas. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Middlewares transversales de autenticación, autorización por rol y aislamiento por inquilino/CUIL. |
| **Esfuerzo** | Medio | Configuración de seguridad en Express, guardias en Angular y pruebas automatizadas de permisos (401/403). |
| **Incertidumbre** | Nula | Prácticas estándar de seguridad en APIs REST corporativas y gubernamentales. |

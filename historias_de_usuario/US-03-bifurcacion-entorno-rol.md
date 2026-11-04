# [ID: US-03] Bifurcación automática de entorno según rol institucional (Aspirante / Vocal)

- **Épica:** E01 - Identidad Digital, Autenticación y Sesión (CiDi)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** usuario autenticado con CiDi Nivel 2,  
> **quiero** que el sistema identifique automáticamente mi rol institucional (docente aspirante o vocal de Junta) y me dirija a mi entorno de trabajo correspondiente,  
> **para** visualizar únicamente las herramientas y módulos habilitados para mis funciones dentro del ciclo administrativo.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio:** Separación estricta de entornos operativos entre el perfil `aspirante` (autogestión docente) y el perfil `vocal` (fiscalización de la Junta de Clasificación), según ADR-014 y ADR-031.
- **Conexión de Datos:** Padrón de vocales habilitados de la Junta de Clasificación y padrón de aspirantes.

## Criterios de Aceptación
1. **Debe** determinar el rol institucional del usuario autenticado (`aspirante` o `vocal`) al momento de procesar su ingreso por CUIL.
2. **Debe** incluir el rol resuelto dentro de los *claims* firmados del token `JWT` de sesión.
3. **Debe** redirigir de forma automática al panel de inicio del portal de autogestión (`/home`) cuando el usuario posea el rol `aspirante`.
4. **Debe** redirigir de forma automática a la bandeja de fiscalización (`/junta`) cuando el usuario posea el rol `vocal`.
5. **Debe** adaptar dinámicamente la barra de navegación (`app-shell`) para mostrar únicamente las secciones permitidas para el rol activo.
6. **Debe** aplicar guardias de ruta en el frontend (`aspiranteGuard` y `vocalGuard`) y middlewares de autorización en el backend (`requireRole`) que devuelvan HTTP 403 si un rol intenta acceder a recursos del otro.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar el ingreso con un CUIL de rol `aspirante` y verificar la redirección automática a `/home` con el menú de Legajo, Formulario C, Puntaje, Mapa y Simulador. *(Pasa)*
- **CP-02:** Probar el ingreso con un CUIL de rol `vocal` y verificar la redirección automática a `/junta` con la vista de escuelas asignadas. *(Pasa)*
- **CP-03:** Probar navegar manualmente a `/junta` estando autenticado como `aspirante` y verificar que el sistema bloquea el acceso (403 / redirección). *(Falla controlada)*
- **CP-04:** Probar invocar el endpoint `GET /api/junta/escuelas` con el token `JWT` de un `aspirante` y verificar que el servidor responde HTTP 403. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Control de acceso basado en roles (RBAC) sincronizado entre rutas SSR/SPA de Angular y middlewares de Express. |
| **Esfuerzo** | Medio | Implementación de `aspiranteGuard`, `vocalGuard`, `requireRole` y menú contextual dinámico. |
| **Incertidumbre** | Baja | Roles claramente acotados en el alcance funcional del proyecto. |

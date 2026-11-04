# [ID: US-04] Expiración por inactividad y cierre seguro de sesión

- **Épica:** E01 - Identidad Digital, Autenticación y Sesión (CiDi)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** usuario con sesión activa en el portal,  
> **quiero** poder cerrar mi sesión manualmente en cualquier momento y que el sistema la finalice de manera automática tras 30 minutos de inactividad,  
> **para** resguardar la privacidad de mi información personal y laboral cuando opero en equipos compartidos o institucionales.

## Reglas de Negocio y Conexiones Externas
- **Regla de Seguridad:** Tiempo máximo de inactividad de sesión fijado en 30 minutos (ADR-005, ADR-031).

## Criterios de Aceptación
1. **Debe** monitorear la actividad del usuario en la interfaz y el tiempo de vida (`exp`) del token `JWT`.
2. **Debe** invalidar y cerrar automáticamente la sesión activa tras **30 minutos continuos de inactividad**, limpiando el estado local del navegador.
3. **Debe** ofrecer un botón visible de **«Cerrar sesión»** en la barra superior del portal para todos los roles.
4. **Debe** eliminar el token `JWT` y cualquier dato temporal de sesión almacenado en el cliente al ejecutar el cierre manual o por inactividad.
5. **Debe** impedir que el usuario vuelva a visualizar pantallas privadas utilizando el botón «Atrás» del navegador una vez cerrada la sesión.
6. **Debe** informar al usuario con un mensaje claro cuando su sesión haya caducado por tiempo de inactividad al intentar realizar una nueva acción.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar presionar el botón «Cerrar sesión» desde cualquier pantalla privada y verificar que se limpia la sesión y ya no se permite navegar a `/legajo` sin volver a autenticarse. *(Pasa)*
- **CP-02:** Probar realizar una petición con un token cuya marca temporal superó los 30 minutos de vigencia y verificar que el sistema rechaza la operación informando sesión expirada. *(Falla controlada)*
- **CP-03:** Probar cerrar sesión y presionar el botón «Atrás» del navegador para intentar ver el Formulario C anterior, verificando que los guardias de ruta impiden el acceso. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Gestión de expiración de JWT, interceptor de errores 401 y detector de inactividad en el cliente. |
| **Esfuerzo** | Bajo | Configuración de TTL de tokens y limpieza de almacenamiento de sesión en el servicio de autenticación. |
| **Incertidumbre** | Nula | Mecanismo estándar en aplicaciones web SPA con autenticación basada en tokens. |

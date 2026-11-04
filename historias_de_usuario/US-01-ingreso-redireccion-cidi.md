# [ID: US-01] Ingreso por redirección desde Ciudadano Digital (CiDi)

- **Épica:** E01 - Identidad Digital, Autenticación y Sesión (CiDi)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** usuario del portal (docente aspirante o vocal de Junta),  
> **quiero** ingresar al sistema de forma directa al ser redirigido desde el portal oficial de Ciudadano Digital (CiDi) con mi sesión provincial activa,  
> **para** operar en la plataforma con mi identidad digital unificada sin necesidad de gestionar usuarios ni contraseñas adicionales.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Plataforma provincial Ciudadano Digital (CiDi / CiDi Test) mediante protocolo OAuth 2.0 / intercambio de ticket de sesión y emisión de token `JWT` propio del portal (ADR-005, ADR-031).
- **Regla de Acceso:** El portal GID no posee formulario de registro ni pantalla de login propia en entorno productivo; actúa como aplicación consumidora de la identidad provista por CiDi.

## Criterios de Aceptación
1. **Debe** recibir el token o código de autorización enviado en la redirección desde Ciudadano Digital (CiDi) al ingresar al portal.
2. **Debe** validar la autenticidad y vigencia del comprobante de sesión contra el servicio de identidad de CiDi en el backend.
3. **Debe** recuperar los atributos básicos de identidad del ciudadano autenticado: CUIL, nombre, apellido, correo electrónico y nivel de cuenta CiDi.
4. **Debe** emitir un token de sesión firmado (`JWT`) con tiempo de expiración acotado para autorizar las peticiones subsiguientes hacia la API REST del portal.
5. **Debe** rechazar cualquier intento de acceso directo a rutas privadas sin un token `JWT` válido y redirigir al punto de entrada de autenticación provincial.
6. **No debe** almacenar ni registrar en logs contraseñas, secretos de cliente ni tokens de sesión completos en texto plano.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar el ingreso al portal viniendo desde una redirección válida de CiDi y verificar que se inicia la sesión mostrando el nombre y CUIL del usuario en el encabezado. *(Pasa)*
- **CP-02:** Probar acceder mediante URL directa a una ruta protegida (`/legajo` o `/formulario-c`) sin sesión iniciada y verificar el rechazo de acceso (HTTP 401) y redirección al flujo de autenticación. *(Falla controlada)*
- **CP-03:** Probar enviar un token `JWT` alterado o con firma inválida a un endpoint de la API y verificar que el backend deniega la petición. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Validación criptográfica de tokens, flujo OAuth 2.0 y middlewares de seguridad en backend y frontend. |
| **Esfuerzo** | Alto | Implementación de guardias de rutas en Angular (`AuthGuard`), interceptores HTTP y emisión/verificación de JWT en Node.js. |
| **Incertidumbre** | Media | Coordinación de parámetros de redirección y certificados con el entorno de homologación CiDi Test. |

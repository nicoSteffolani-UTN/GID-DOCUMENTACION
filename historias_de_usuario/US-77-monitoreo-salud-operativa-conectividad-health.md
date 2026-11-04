# [ID: US-77] Monitoreo de salud operativa y diagnóstico de conectividad a bases de datos (`/api/health`)

- **Épica:** Sin Épica (Transversal - Operación e Interoperabilidad)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 2 SP
- **Rol / Actor:** Administrador de Infraestructura / Sistema

## Descripción (Narrativa)
> **Como** administrador de infraestructura y operaciones,  
> **quiero** disponer de un endpoint de diagnóstico de salud (`/api/health`) que informe el estado del servidor backend, el modo de integración activo y la conectividad con PostgreSQL y Oracle,  
> **para** supervisar la disponibilidad del servicio en contenedores Docker y entornos de despliegue continuo.

## Reglas de Negocio y Conexiones Externas
- **Operación y Despliegue (`Instancia 5 §3`, `compose.dev.yaml`, `render.yaml`):** Permite que balanceadores de carga, orquestadores de contenedores y administradores verifiquen que la API y sus almacenes de datos se encuentran operativos.

## Criterios de Aceptación
1. **Debe** responder en `GET /api/health` con código `HTTP 200` y un objeto JSON estructurado cuando el servicio se encuentre operativo.
2. **Debe** informar en la respuesta: el estado general (`ok`), marca temporal actual del servidor, modo de integración configurado (`prod` o `mock`) y estado de conexión de la persistencia PostgreSQL / Oracle.
3. **No debe** exponer en la respuesta de salud cadenas de conexión, usuarios de base de datos, direcciones IP internas sensibles ni contraseñas.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar realizar una petición `GET /api/health` con el servidor en ejecución y verificar que responde `HTTP 200` en menos de 500 ms con el estado del sistema. *(Pasa)*
- **CP-02:** Probar verificar que el payload de `/api/health` no incluye secretos ni variables sensibles del entorno. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Endpoint ligero de verificación de estado de aplicación y pools de conexiones. |
| **Esfuerzo** | Bajo | Controlador de healthcheck e integración en `docker-compose` / plataforma de despliegue. |
| **Incertidumbre** | Nula | Práctica estándar de observabilidad. |

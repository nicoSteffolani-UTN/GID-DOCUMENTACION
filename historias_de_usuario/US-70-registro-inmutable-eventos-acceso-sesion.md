# [ID: US-70] Registro inmutable de eventos de acceso y sesión conforme a estándares institucionales

- **Épica:** E14 - Gobernanza, Seguridad y Auditoría Inmutable
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Sistema / Autoridad de Auditoría Ministerial

## Descripción (Narrativa)
> **Como** responsable de seguridad y cumplimiento normativo del Ministerio de Educación,  
> **quiero** que el portal registre de manera automática e inalterable cada evento de ingreso, cierre de sesión y validación de nivel de identidad CiDi,  
> **para** mantener en los nuevos servicios web los mismos estándares de trazabilidad y control de accesos que rigen en la infraestructura provincial.

## Reglas de Negocio y Conexiones Externas
- **Alineación Institucional (`ADR-032`):** En continuidad con las políticas de seguridad y auditoría del Ministerio de Educación y de la Junta de Clasificación, la tabla `auditoria_acceso` en PostgreSQL opera bajo modalidad estricta **`APPEND-ONLY`** (solo inserción de eventos, sin operaciones de edición ni borrado).

## Criterios de Aceptación
1. **Debe** insertar un registro en `auditoria_acceso` ante cada evento de sesión relevante: ingreso exitoso (`LOGIN_OK`), cierre de sesión (`LOGOUT`), bloqueo por nivel insuficiente (`LOGIN_NIVEL_1_RECHAZADO`) e intento de autenticación inválido (`LOGIN_FALLIDO`).
2. **Debe** almacenar en cada evento: identificador único, fecha y hora exacta del servidor (`TIMESTAMPTZ`), número de CUIL involucrado, rol resuelto, tipo de evento y metadatos técnicos del acceso (dirección IP / User-Agent, sin almacenar credenciales ni tokens en claro).
3. **No debe** exponer ningún método en el repositorio de datos ni endpoint en la API que permita modificar (`UPDATE`) o eliminar (`DELETE`) registros de la tabla `auditoria_acceso`.
4. **Debe** ejecutar el registro de auditoría de forma transparente sin degradar el tiempo de respuesta del ingreso del usuario.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar realizar un ingreso exitoso y un cierre de sesión, verificando que ambos eventos quedan grabados cronológicamente en `auditoria_acceso` con el CUIL, rol y marca temporal. *(Pasa)*
- **CP-02:** Probar un intento de ingreso con una cuenta de CiDi Nivel 1 y comprobar que queda asentado el evento de rechazo por nivel normativo en la bitácora de accesos. *(Pasa)*
- **CP-03:** Probar verificar la ausencia de operaciones de borrado o actualización sobre el repositorio de auditoría de accesos. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Persistencia `APPEND-ONLY` en PostgreSQL disparada desde el servicio de autenticación (ADR-032). |
| **Esfuerzo** | Bajo | Modelo `auditoria_acceso` e integración con los eventos de login/logout. |
| **Incertidumbre** | Nula | Implementado en el módulo `auditoria` del backend. |

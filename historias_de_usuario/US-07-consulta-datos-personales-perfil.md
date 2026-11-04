# [ID: US-07] Consulta de datos personales e institucionales en Mi Perfil

- **Épica:** E02 - Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 2 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** consultar mis datos filiatorios, de contacto y de titulación principal registrados en mi perfil institucional,  
> **para** verificar que mi identificación personal y académica esté correctamente asociada a mi cuenta antes de realizar presentaciones ante la Junta.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Datos de identidad provenientes de CiDi y registros de perfil docente (ADR-030).
- **Regla de Negocio:** Conforme a la decisión de alcance (ADR-013), el perfil del docente **no requiere ni incluye la selección de una "escuela base"**, quedando desacoplado de establecimientos ancla históricos.

## Criterios de Aceptación
1. **Debe** mostrar en la sección de perfil del docente su Nombre completo, Apellido, número de CUIL formateado, correo electrónico y nivel de verificación CiDi.
2. **Debe** exponer el título o títulos habilitantes principales asociados a su legajo que se utilizan como base para el validador de alcances.
3. **Debe** impedir la edición manual de datos filiatorios primarios (CUIL, nombre y apellido) desde el portal, indicando que dichos datos provienen de la identidad oficial de Ciudadano Digital.
4. **No debe** exigir ni mostrar campos de selección de «Escuela Base» en el perfil del aspirante (ADR-013).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar acceder a la vista de perfil de un docente autenticado y verificar que se visualizan su CUIL, nombre completo, correo y título principal registrado. *(Pasa)*
- **CP-02:** Probar verificar que los campos de identidad oficial (CUIL y nombre) se presentan en modo de solo lectura y no admiten alteración por formulario o API. *(Pasa)*
- **CP-03:** Probar consultar el perfil sin sesión activa y verificar que el endpoint devuelve HTTP 401. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Lectura y presentación de datos del perfil del usuario autenticado. |
| **Esfuerzo** | Bajo | Endpoint de perfil y componente visual en Angular. |
| **Incertidumbre** | Nula | Estructura de datos definida y alineada con ADR-013 y ADR-030. |

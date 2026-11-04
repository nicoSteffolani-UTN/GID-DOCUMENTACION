# [ID: US-05] Arnés de ingreso simulado temporal para entornos de desarrollo y homologación académica

- **Épica:** E01 - Identidad Digital, Autenticación y Sesión (CiDi)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Equipo de Desarrollo / Evaluador Académico

## Descripción (Narrativa)
> **Como** integrante del equipo de desarrollo o evaluador en entorno de prueba,  
> **quiero** contar con una pasarela de acceso simulada temporal que emule la respuesta de Ciudadano Digital con perfiles precargados,  
> **para** poder ejecutar pruebas integrales y demostraciones funcionales de todos los escenarios de negocio cuando no se opera desde la red interna de producción de CiDi.

## Reglas de Negocio y Conexiones Externas
- **Condición Arquitectónica:** Solución estrictamente temporal de desarrollo y demostración (ADR-005, ADR-026, ADR-031). En operación productiva el acceso ocurre exclusivamente por redirección externa de CiDi; el arnés local solo emula el *handshake* de retorno para permitir probar distintos CUIL y roles (`aspirante` con puntaje, `aspirante` sin valoración, cuenta Nivel 1 bloqueada y distintos `vocales` para verificar aislamiento de escuelas).

## Criterios de Aceptación
1. **Debe** proveer un punto de entrada de prueba (`/login` / adaptador mock de CiDi) habilitado únicamente bajo configuración de entorno de desarrollo o demostración académica.
2. **Debe** permitir seleccionar o ingresar los perfiles de prueba documentados (aspirantes con distintos estados de legajo, vocales de distintas jurisdicciones y usuario Nivel 1) para simular el retorno de autenticación de CiDi.
3. **Debe** generar exactamente el mismo formato de token `JWT` y estructura de sesión que el flujo de redirección de CiDi, de modo que el resto de los módulos del sistema operen sin código condicional.
4. **Debe** permitir desactivar esta pantalla temporal mediante configuración de entorno cuando el portal se despliega integrado directamente detrás del redireccionamiento oficial de Ciudadano Digital.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar autenticar en el entorno de demostración con el CUIL de prueba `20123456789` (aspirante) y verificar que recibe un token `JWT` idéntico al del contrato de sesión e ingresa al flujo completo. *(Pasa)*
- **CP-02:** Probar alternar en la misma sesión de demostración entre los vocales de prueba `27999888777` y `27111222333` para comprobar el aislamiento de escuelas sin requerir múltiples cuentas físicas de CiDi. *(Pasa)*
- **CP-03:** Probar ingresar una credencial inexistente en el arnés de prueba y verificar que devuelve error de autenticación controlada. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Patrón de Puertos y Adaptadores para desacoplar el proveedor de identidad del núcleo de sesión. |
| **Esfuerzo** | Bajo | Vista de selección/ingreso temporal y fixture de identidades de prueba (`usuarios.mock.json`). |
| **Incertidumbre** | Nula | Control total sobre los datos sintéticos de prueba. |

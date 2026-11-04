# [ID: US-75] Conmutación transparente de modo de integración (Oracle Lab Productivo vs. Modo Mock) y rótulos de transparencia

- **Épica:** Sin Épica (Transversal - Operación e Interoperabilidad)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Equipo de Desarrollo / Evaluador / Administrador Técnico

## Descripción (Narrativa)
> **Como** administrador técnico o evaluador del sistema,  
> **quiero** que el backend permita conmutar mediante configuración entre la conexión en vivo al laboratorio Oracle (`INTEGRATION_MODE=prod`) y el juego de datos sintéticos locales (`INTEGRATION_MODE=mock`), mostrando rótulos visuales de transparencia cuando un dato sea de ejemplo,  
> **para** asegurar que el sistema pueda evaluarse tanto conectado a la VPN ministerial como en entornos académicos aislados sabiendo en todo momento el origen de cada dato.

## Reglas de Negocio y Conexiones Externas
- **Arquitectura de Puertos y Adaptadores (`ADR-026`, `DATOS_REALES_Y_SIMULADOS.md`):**
  - El sistema conecta por defecto a los datos reales del laboratorio Oracle (`prod`) para el padrón de escuelas, catálogo del Formulario C, alcances de títulos (74.765 combinaciones), mapa de vacantes (`VT_PUBLI_EMPRE_MAT_ASPIRANTES`), estadísticas de LOM (`T_DETALLES_INSC_JD`) y antigüedad (`PEOPLENET`).
  - Cuando algún componente opera con datos simulados o de demostración, la interfaz muestra un rótulo explícito de **«Datos de ejemplo»** para total claridad institucional.

## Criterios de Aceptación
1. **Debe** resolver en el arranque del servidor qué adaptadores inyectar en cada módulo según la variable de entorno `INTEGRATION_MODE` (`prod` o `mock`).
2. **Debe** exponer en los metadatos de respuesta de la API el origen de la fuente de datos (`oracle-lab` vs `mock` / `estimado`) para cada módulo.
3. **Debe** mostrar en la interfaz de usuario un distintivo o rótulo claro de **«Datos de ejemplo / Demostración»** en aquellas secciones que utilicen registros simulados, diferenciándolas de las consultas en vivo a la base Oracle del Ministerio.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar iniciar el sistema con `INTEGRATION_MODE=prod` y conexión VPN activa, verificando que el validador, el padrón del Formulario C y el mapa consumen los registros reales del laboratorio Oracle. *(Pasa)*
- **CP-02:** Probar iniciar el sistema con `INTEGRATION_MODE=mock` (sin VPN) y verificar que todos los flujos funcionan de punta a punta exhibiendo los rótulos correspondientes de datos de demostración. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Inyección de dependencias mediante patrón Puertos y Adaptadores en todos los módulos del backend (ADR-026). |
| **Esfuerzo** | Medio | Implementación dual de repositorios (`*.oracle.repository.ts` y `*.mock.repository.ts`) e indicadores en UI. |
| **Incertidumbre** | Nula | Mitigó exitosamente el riesgo R2 del proyecto y está documentado en `DATOS_REALES_Y_SIMULADOS.md`. |

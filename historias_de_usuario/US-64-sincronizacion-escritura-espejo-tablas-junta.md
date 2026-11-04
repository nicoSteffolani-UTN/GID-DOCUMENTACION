# [ID: US-64] Sincronización y escritura espejo en las tablas de la Junta en entornos de desarrollo y prueba

- **Épica:** E12 - Gestión y Fiscalización para Vocales de Junta
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Sistema / Administrador de Base de Datos

## Descripción (Narrativa)
> **Como** arquitecto del sistema y equipo técnico del Ministerio,  
> **quiero** que las operaciones de confirmación de Formulario C y cambios de legajo se repliquen de forma controlada en una lista cerrada de tablas espejo de la Junta cuando el entorno de base de datos habilite escritura (`DEV` / `TEST`),  
> **para** mantener la interoperabilidad con el esquema relacional de la Junta de Clasificación mientras se preserva el carácter de solo lectura sobre los catálogos maestros de producción.

## Reglas de Negocio y Conexiones Externas
- **Decisión Arquitectónica (`ADR-028`, `ADR-029`):**
  - El portal persiste su estado operativo en **PostgreSQL** (`antecedente_usuario`, `formulario_c_usuario`, `interes_vacante`, `auditoria_*`).
  - Cuando se opera contra un entorno Oracle con permisos de escritura habilitados (`DEV`/`TEST`), el adaptador sincroniza exclusivamente sobre una **lista blanca cerrada** de tablas de la Junta (`T_INSCRIPCIONES`, `T_FORMULARIO_C` / detalle de antecedentes), sin tocar jamás tablas de padrón ni vistas de `PEOPLENET`.

## Criterios de Aceptación
1. **Debe** persistir toda operación de legajo, Formulario C y dictamen de vocal en el motor PostgreSQL del portal de forma atómica.
2. **Debe** replicar en segundo plano o dentro del adaptador de escritura los registros de inscripción y antecedentes hacia las tablas habilitadas de la Junta cuando el modo de escritura espejo esté activo en el entorno.
3. **Debe** restringir por código mediante una lista cerrada las tablas de destino permitidas, impidiendo cualquier sentencia `INSERT`, `UPDATE` o `DELETE` sobre vistas de catálogos, alcances o Recursos Humanos.
4. **Debe** continuar operando sin interrupción sobre PostgreSQL cuando la conexión al laboratorio Oracle sea de solo lectura (`MT4D` lab), registrando el estado del espejo en los logs operativos.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar confirmar un Formulario C y validar un antecedente verificando que los datos quedan grabados en PostgreSQL y se ejecuta el adaptador espejo según la configuración del entorno. *(Pasa)*
- **CP-02:** Probar operar contra el laboratorio Oracle de solo lectura y verificar que todas las escrituras del portal se completan exitosamente en PostgreSQL sin arrojar errores de permisos `ORA-01031` al usuario. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Patrón de persistencia dual detrás de interfaz de repositorio con control estricto de tablas permitidas (ADR-028, ADR-029). |
| **Esfuerzo** | Medio | Adaptadores PostgreSQL y sincronizador Oracle para tablas de la Junta. |
| **Incertidumbre** | Media | Diferencia de permisos entre el ambiente de solo lectura `MT4D` y los ambientes de prueba `MT4T`. |

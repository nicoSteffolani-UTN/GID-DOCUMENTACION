# [ID: US-30] Confirmación definitiva de la inscripción anual en el Formulario C

- **Épica:** E06 - Convocatoria Anual e Inscripción en Formulario C
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** confirmar de manera formal mi Formulario C una vez completada mi elección de establecimientos y asignaturas,  
> **para** dejar asentada oficialmente mi postulación en el ciclo lectivo ante la Junta de Clasificación Secundaria.

## Reglas de Negocio y Conexiones Externas
- **Persistencia Dual:** Se registra en PostgreSQL del portal (`formulario_c_usuario`, ADR-028) y se replica en las tablas espejo de la Junta en entornos de desarrollo y prueba (`T_INSCRIPCIONES` / `T_FORMULARIO_C`, ADR-029).
- **Carácter de Declaración Jurada:** La confirmación requiere aceptación explícita del resumen de escuelas y materias seleccionadas.

## Criterios de Aceptación
1. **Debe** presentar un diálogo o vista previa de confirmación que resuma las escuelas seleccionadas, las materias elegidas en cada una y el alcance de título correspondiente antes de ejecutar el envío definitivo.
2. **Debe** validar nuevamente en el servidor todas las reglas de negocio (convocatoria abierta, entre 1 y 3 escuelas, entre 1 y 5 materias por escuela, identidad Nivel 2) al procesar la confirmación (`POST /api/formulario-c`).
3. **Debe** cambiar el estado de la inscripción a **`Confirmado`**, asignando un identificador único de trámite/comprobante y sello temporal del servidor (`timestamp`).
4. **Debe** hacer visible de forma inmediata al aspirante dentro de la nómina de las escuelas elegidas para los vocales de la Junta asignados a dichos establecimientos (`/junta`).

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar confirmar un Formulario C válido, aceptar el diálogo de confirmación y verificar que el estado pasa a `Confirmado` con número de operación y fecha/hora. *(Pasa)*
- **CP-02:** Probar que una vez confirmado el Formulario C en una escuela (ej. `ESC-001`), el vocal asignado a `ESC-001` visualice al aspirante dentro del listado de inscriptos de esa escuela. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Transacción de confirmación, generación de código de operación y vinculación con la bandeja del vocal. |
| **Esfuerzo** | Medio | Flujo de confirmación en UI y lógica transaccional en backend. |
| **Incertidumbre** | Baja | Contrato de datos alineado entre módulos `formulario-c` y `junta`. |

# [ID: US-51] Guardado y comparación de hasta 3 escenarios de simulación por usuario

- **Épica:** E10 - Simulador de Carrera Docente y Recomendador SIPEC
- **Prioridad:** P3 (Baja / Evolución)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** guardar y nombrar hasta 3 escenarios de simulación distintos en mi cuenta para compararlos entre sí,  
> **para** evaluar distintas alternativas de perfeccionamiento (por ejemplo, hacer un posgrado vs. acumular cursos cortos) antes de decidir en qué invertir mi tiempo de formación.

## Reglas de Negocio y Conexiones Externas
- **Límite de Almacenamiento (Instancia 2 §19.12):** Tope máximo estricto de **3 escenarios guardados** por cada cuenta de docente aspirante.

## Criterios de Aceptación
1. **Debe** permitir al docente asignar un nombre descriptivo a su simulación actual y guardarla en su perfil (hasta un máximo de 3 escenarios activos).
2. **Debe** bloquear el guardado de un cuarto escenario mostrando un mensaje claro que invite a eliminar o sobrescribir alguno de los 3 escenarios existentes.
3. **Debe** permitir cargar, comparar lado a lado o eliminar cualquiera de los escenarios guardados.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar guardar un escenario de simulación asignándole el nombre «Plan Posgrado 2027» y verificar que queda listado y disponible para su consulta. *(Pasa)*
- **CP-02:** Probar intentar guardar un cuarto escenario de simulación cuando el usuario ya posee 3 guardados y verificar el bloqueo por límite máximo. *(Falla controlada)*
- **CP-03:** Probar eliminar uno de los 3 escenarios guardados y comprobar que se habilita nuevamente el cupo para guardar uno nuevo. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Persistencia acotada (máx. 3 registros por CUIL) con validación de cupo. |
| **Esfuerzo** | Bajo | Gestión de escenarios en el componente de simulador y almacenamiento asociado al usuario. |
| **Incertidumbre** | Nula | Regla de negocio explícita en los criterios de aceptación de Instancia 2. |

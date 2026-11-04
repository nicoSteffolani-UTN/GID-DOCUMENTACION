# [ID: US-18] Ponderación automática de capacitaciones y títulos según reglas del Decreto 257/19

- **Épica:** E04 - Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Sistema / Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** que el motor de valoración aplique automáticamente las fórmulas del Decreto 257/19 sobre mis títulos, antigüedad y horas de capacitación validadas,  
> **para** asegurar que cada certificado aprobado se traduzca en el puntaje exacto que establece la normativa vigente sin errores de cálculo manual.

## Reglas de Negocio y Conexiones Externas
- **Reglas del Decreto 257/19 (ADR-027):**
  - **Título Base:** Docente = **18 pts**, Habilitante = **12 pts**, Supletorio = **6 pts**.
  - **Capacitaciones:** Se computan únicamente los cursos aprobados con carga horaria **≥ 20 horas reloj**, a razón de **`horas × 0,017` puntos** (equivalente a 0,17 puntos cada 10 horas cátedra), respetando el tope del rubro.
  - **Exclusión de no validados:** Los antecedentes en estado `Pendiente` o `Rechazado` no suman al puntaje firme validado.

## Criterios de Aceptación
1. **Debe** asignar al rubro de título base el puntaje correspondiente a su categoría reglamentaria (18, 12 o 6 puntos).
2. **Debe** calcular el aporte de cada curso de capacitación validado aplicando la fórmula `horas × 0,017` siempre que la carga horaria sea igual o superior a **20 horas reloj**.
3. **No debe** asignar puntaje en el rubro de capacitación general a cursos cuya carga horaria sea inferior al mínimo reglamentario de 20 horas reloj.
4. **Debe** aplicar los topes máximos por rubro definidos en `baremo-decreto-257.json` para que ningún rubro exceda el límite legal permitido.
5. **Debe** recalcular automáticamente el puntaje del docente cuando un vocal de la Junta cambia el estado de un antecedente a `Validado` o `Rechazado`.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar valorar un curso validado de 40 horas reloj y verificar que el motor le asigna `40 × 0,017 = 0,68` puntos dentro del rubro de capacitación. *(Pasa)*
- **CP-02:** Probar valorar un curso de 12 horas reloj (menor al mínimo de 20 h) y comprobar que el motor le asigna `0,00` puntos según la regla normativa. *(Pasa)*
- **CP-03:** Probar que un antecedente en estado `Rechazado` no sume puntos al total validado del aspirante. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Fórmulas aritméticas con redondeo controlado a 2 decimales y evaluación de topes acumulativos. |
| **Esfuerzo** | Medio | Implementación de reglas de dominio unitariamente testeadas en el módulo de puntaje y valoración. |
| **Incertidumbre** | Nula | Parámetros matemáticos explicitados en el Decreto 257/19 y ADR-027. |

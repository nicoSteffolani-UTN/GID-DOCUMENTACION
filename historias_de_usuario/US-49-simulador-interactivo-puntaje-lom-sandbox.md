# [ID: US-49] Simulador interactivo de puntaje LOM en entorno aislado (Sandbox Dto. 257/19)

- **Épica:** E10 - Simulador de Carrera Docente y Recomendador SIPEC
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** simular en tiempo real cómo variaría mi puntaje de la LOM agregando cursos hipotéticos, años adicionales de antigüedad o nuevas titulaciones/posgrados en un entorno aislado,  
> **para** planificar estratégicamente mi formación profesional sin alterar en ningún momento los datos reales de mi legajo oficial.

## Reglas de Negocio y Conexiones Externas
- **Entorno Sandbox (`ADR-011`, `ADR-024`, `/simulador`):** Parte del puntaje actual del docente y aplica las reglas y topes del Decreto 257/19 sobre variables hipotéticas en memoria/escenario sin modificar `antecedente_usuario` ni `T_INSCRIPCIONES`.

## Criterios de Aceptación
1. **Debe** inicializar el simulador (`/simulador`, `POST /api/simulador/calcular`) tomando como línea base el puntaje actual del docente desglosado por rubros del Decreto 257/19.
2. **Debe** permitir sumar de forma interactiva: cursos de capacitación (del catálogo o indicando carga horaria ≥ 20 h), años enteros positivos de antigüedad docente y títulos/posgrados adicionales.
3. **Debe** calcular de inmediato el **Nuevo Puntaje Simulado** y mostrar el incremento neto (`+X,XX puntos`) respetando los topes máximos de cada rubro reglamentario.
4. **Debe** admitir únicamente valores numéricos enteros y positivos para los años de antigüedad y horas de capacitación simuladas.
5. **Debe** garantizar de forma física y lógica que ninguna acción ejecutada dentro del simulador altere los registros reales del legajo ni la inscripción oficial del docente.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar agregar en el simulador una capacitación de 50 horas reloj y 2 años adicionales de antigüedad, verificando que el puntaje simulado se incrementa en tiempo real y el legajo real en `/legajo` permanece intacto. *(Pasa)*
- **CP-02:** Probar ingresar un valor negativo o caracteres alfabéticos en los años de antigüedad simulados y verificar que el sistema rechaza el valor. *(Falla controlada)*
- **CP-03:** Probar simular capacitaciones que superen el tope máximo del rubro según el Decreto 257/19 y comprobar que el simulador aplica el tope legal e informa que el rubro alcanzó su límite. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Reutilización del motor matemático del Decreto 257/19 sobre un estado aislado (sandbox) en memoria. |
| **Esfuerzo** | Medio | Interfaz interactiva de simulación en Angular y endpoint de proyección en Node.js. |
| **Incertidumbre** | Nula | Reglas del baremo ya implementadas en el módulo de valoración. |

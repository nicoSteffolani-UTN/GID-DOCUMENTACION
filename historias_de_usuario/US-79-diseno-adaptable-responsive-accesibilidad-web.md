# [ID: US-79] Diseño adaptable (Responsive) y criterios de accesibilidad web para docentes y vocales

- **Épica:** Sin Épica (Transversal - Operación e Interoperabilidad)
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** docente aspirante o vocal de la Junta que accede desde distintos dispositivos (computadora de escritorio, notebook, tablet o teléfono móvil),  
> **quiero** que todas las pantallas del portal adapten su disposición visual, mantengan alto contraste de lectura y acompañen los colores con etiquetas textuales e íconos,  
> **para** poder consultar mi puntaje, explorar el mapa, revisar mi legajo o descargar comprobantes con comodidad y accesibilidad.

## Reglas de Negocio y Conexiones Externas
- **Estándares de Interfaz (`Instancia 2 §12`, `ADR-001`, `ADR-008`):** Textos íntegros en español claro, paleta institucional con contraste legible y etiquetas textuales además de color en todos los indicadores de estado y semáforos.

## Criterios de Aceptación
1. **Debe** adaptar la estructura de navegación (`app-shell`), tablas, tarjetas y formularios a resoluciones de escritorio (≥ 1024px), tablet (768px) y dispositivos móviles (360px–480px) sin pérdida de funcionalidad.
2. **Debe** acompañar todos los indicadores basados en color (como el Semáforo LOM `Verde`/`Amarillo`/`Rojo` y los estados de antecedente `Validado`/`Pendiente`/`Rechazado`) con texto explícito o iconografía distintiva para usuarios con dificultades en la percepción del color.
3. **Debe** permitir la navegación por teclado (`Tab`, `Enter`, `Escape`) en formularios, diálogos modales y selectores principales.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar visualizar las pantallas de `/home`, `/legajo`, `/formulario-c`, `/puntaje` y `/mapa` en una resolución móvil (375×812 px) y verificar que el menú y los controles se operan correctamente sin desbordamientos horizontales rotos. *(Pasa)*
- **CP-02:** Probar verificar que cada marcador o insignia de color (semáforo y estado de trámite) incluye su etiqueta textual correspondiente. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Maquetación con CSS Grid / Flexbox, *media queries* y atributos semánticos de accesibilidad (`aria-label`). |
| **Esfuerzo** | Medio | Ajuste responsivo transversal en todas las vistas y componentes de Angular. |
| **Incertidumbre** | Nula | Criterios de diseño aplicados en la capa de presentación del portal. |

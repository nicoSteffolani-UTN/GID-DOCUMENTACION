# ADR-025: Ayudas para decidir (Formulario C, antecedentes pendientes, vacantes y radio de viaje)

**Estado:** Aceptado  
**Fecha:** 2026-10-05  
**Relacionado:** ADR-009 (alcance de título), ADR-010 (motor LOM), ADR-019 (semáforo LOM del mapa), ADR-020 (vacantes), ADR-022 (asistente), ADR-024 (simulador de carrera)

## Contexto

El portal ya le muestra al docente su puntaje, el mapa con el promedio histórico de cada escuela, las vacantes publicadas y un
simulador de carrera. Pero las decisiones que más le importan las sigue tomando a ciegas: **a qué tres escuelas inscribirse** en el
Formulario C, **cuánto le cambia** que la junta acepte lo que cargó y **cuál de las vacantes publicadas le conviene** mirar primero.
Además, el docente piensa en «hasta dónde puedo viajar», no en localidades sueltas.

## Decisión

Se agregan cuatro ayudas de solo lectura que reutilizan los mismos datos y el mismo criterio de niveles del simulador (ADR-024).
Ninguna guarda nada ni habla de probabilidades: comparan el puntaje del docente con el promedio histórico de cada escuela y un
**margen de seguridad** (3 puntos por defecto, de 0 a 20).

| Nivel | Regla (P = puntaje, M = promedio de la escuela, m = margen) |
|---|---|
| Segura | M + m ≤ P |
| Justa | M ≤ P < M + m |
| Apuesta | P < M ≤ P + 10 |
| Lejos | M > P + 10 |
| Sin datos | la escuela no tiene promedio o el docente no tiene puntaje |

### 1. Armá tu Formulario C

- **Propuesta.** `GET /api/formulario-c/propuesta` propone hasta tres escuelas: una **segura**, una **justa** y una **apuesta**, entre
  las del mapa con promedio que están en el padrón de inscripción y dictan materias que habilitan sus títulos. Prioriza las que tienen
  vacantes abiertas, después las más cercanas (si eligió una zona) y las que ofrecen más materias. Si falta un rol, completa con las
  que quedan y lo avisa. En cada escuela sugiere hasta cinco materias o cargos: primero los que tienen vacante abierta, después los de
  condición docente y por último los supletorios. Cada escuela trae sus motivos en texto.
- **Usar la propuesta.** El docente la carga en el formulario con un botón (pide confirmación si ya tenía escuelas). **No se guarda**:
  la revisa y toca «Guardar inscripción» como siempre.
- **Revisión en vivo.** `POST /api/formulario-c/revision` recibe la inscripción que está en pantalla (sin guardarla) y devuelve
  hallazgos ordenados: alertas (escuelas sin materias, materias que sus títulos no habilitan, escuelas lejos de su puntaje, ninguna a
  su alcance), sugerencias (lugares libres con materias sugeridas, las tres seguras, menos de tres escuelas) y lo que está bien
  (vacantes abiertas en lo elegido). La pantalla la pide sola, con una espera de 400 ms, cada vez que el docente cambia algo, y muestra
  el nivel de cada escuela en su panel.

### 2. Cuánto valen mis antecedentes pendientes

`GET /api/simulador/pendientes` estima qué pasaría si la junta aceptara hoy todo lo pendiente: puntaje antes y después, escuelas a
cuyo promedio llegaría (con hasta tres ejemplos) y materias nuevas que le habilitarían. Por cada antecedente:

- **Título:** cuenta las materias o cargos que habilita y hoy no tiene. Con puntaje estimado suma lo que sube el mejor estimado
  (título y acumulación); con valoración LOM oficial no se estiman puntos, porque la valoración ya contempla sus títulos.
- **Los demás antecedentes:** lo que sube el mejor estimado al sumarlo, con el artículo del Dto. 257/19 que lo valora (ADR-027);
  la antigüedad, hasta el tope de 10. Si falta un dato, «sin estimar» con el motivo; si el decreto no lo valora, «no suma».

El total se calcula con todos juntos (el tope de antigüedad hace que no sea la suma de cada uno). Se muestra en **Mi puntaje**, dentro
del aviso de antecedentes pendientes, y en **Mi legajo** como chips en cada antecedente pendiente («Suma ~X pts si la aceptan»,
«Habilita N materias nuevas»).

### 3. Radio de viaje

Un selector de zona reutilizable (`SelectorZonaComponent` y `zona-util.ts`) permite elegir una localidad del mapa o la ubicación del
navegador y un radio de 5 a 150 km (30 por defecto). La distancia es en línea recta (fórmula de Haversine), no por ruta. Lo usan:

- el **simulador** (ADR-024): todas las cuentas, el mapa (con el círculo de la zona) y el plan exportado se limitan a esa zona;
- la **propuesta del Formulario C**: con `lat`, `lng`, `radioKm` y `zona` solo propone escuelas dentro del radio.

La ubicación del navegador se usa una sola vez para el cálculo y no se guarda.

### 4. Vacantes ordenadas por cuánto te convienen

`GET /api/simulador/ajuste-vacantes` devuelve, para cada vacante publicada, si sus títulos la habilitan (y con qué condición), su
puntaje para esa materia (la fila LOM si existe, si no el estimado), el promedio de la escuela, la diferencia, el nivel y un motivo en
texto. Vienen ordenadas de la que más le conviene a la que menos (segura, justa, apuesta, lejos, sin datos, no habilitada). En
**Alertas de vacantes** cada vacante abierta muestra su nivel con el motivo, hay un resumen («de las 12 abiertas, 2 son seguras…») y un
orden «Las que más me convienen» que el navegador recuerda.

### Dónde vive el código

- `formulario-c.estrategia.ts` (módulo formulario-c): propuesta, revisión y la regla `nivelEscuela` que usan las demás ayudas.
- `decisiones.service.ts` (módulo simulador): pendientes y ajuste de vacantes. Está en el simulador y no en puntaje o notificaciones
  porque necesita los datos del asistente (`AsistenteDatos`), que a su vez usa esos servicios: ponerlo ahí armaría un ciclo de imports.
- `PuntajeService.componentesDe` calcula las partes del estimado para cualquier lista de antecedentes (aceptados, o aceptados más
  pendientes) con la misma lógica que el puntaje de siempre.

## Alternativas consideradas

- **Probabilidad de quedar en cada escuela:** se descartó. Los promedios son históricos y generales, sin datos de variación ni de
  cuántos docentes se presentan; un porcentaje daría una precisión que no existe. Se usan niveles con un margen que el docente entiende.
- **Guardar la propuesta directamente:** se descartó; inscribirse es una decisión del docente, y la propuesta puede no considerar
  preferencias que el sistema no conoce (turnos, horarios, otras escuelas). Solo se carga en pantalla.
- **Distancia por calles o tiempo de viaje:** requeriría un servicio de ruteo externo; la línea recta alcanza para acotar y no saca
  datos del portal.
- **Estimar los títulos con valoración LOM:** se descartó para no mezclar la estimación del Dto. 257/19 con una valoración oficial cuyo
  desglose no se conoce.

## Consecuencias

**Positivas**

- El docente arma una inscripción equilibrada en un paso y ve en vivo qué le conviene corregir.
- Sabe qué gana si la junta acepta lo pendiente y por qué conviene completar los datos (horas, años) de lo que cargó.
- Prioriza las vacantes por su situación real y no solo por fecha.

**Negativas / limitaciones**

- Todo depende del promedio histórico y general de cada escuela (no hay promedio por materia) y de la estimación del Dto. 257/19 (ADR-027).
- Los antecedentes cuyos datos no se pueden leer de lo cargado quedan «sin estimar».
- La distancia es en línea recta.

## Referencias

- Código: `src/server/src/modules/formulario-c/formulario-c.estrategia.ts`, `src/server/src/modules/simulador/decisiones.service.ts`,
  `src/web/src/app/shared/selector-zona.component.ts`, `src/web/src/app/shared/zona-util.ts`, pantallas `formulario-c`, `puntaje`,
  `legajo`, `notificaciones` y `simulador`
- Tests: `formulario-c.estrategia.test.ts`, `decisiones.service.test.ts`, `__tests__/formulario-c-estrategia.test.ts`,
  `__tests__/decisiones.test.ts`, `zona-util.spec.ts`
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml)

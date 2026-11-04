# [ID: US-17] Consulta del puntaje total de inscripción y desglose por rubro normativo (Dto. 257/19)

- **Épica:** E04 - Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** consultar en línea mi puntaje total y su desglose detallado por cada rubro reglamentario del Decreto Provincial Nº 257/19,  
> **para** comprender con exactitud cómo se compone mi calificación (título, acumulación, antigüedad, capacitaciones y otros antecedentes) antes de elegir establecimientos en la convocatoria anual.

## Reglas de Negocio y Conexiones Externas
- **Normativa:** Baremo oficial del Decreto Provincial Nº 257/19 (ADR-010, ADR-027, `baremo-decreto-257.json`).
- **Integración Externa:** Consulta de valoración en laboratorio Oracle (`T_DETALLES_INSC_JD` / vistas de inscripción) y cálculo analítico sobre antecedentes validados del legajo.

## Criterios de Aceptación
1. **Debe** exponer en la pantalla `/puntaje` (`GET /api/puntaje/inscripcion`) el puntaje total consolidado del docente expresado con precisión decimal reglamentaria (2 decimales).
2. **Debe** presentar el desglose discriminado por cada rubro normativo del Decreto 257/19:
   - **Título Principal** (según categoría: Docente, Habilitante o Supletorio).
   - **Acumulación de Títulos / Posgrados**.
   - **Antigüedad Docente**.
   - **Capacitación y Formación Continua**.
   - **Otros Antecedentes / Concepto y Promedio**.
3. **Debe** indicar junto a cada rubro el tope máximo permitido por la reglamentación vigente y el porcentaje o margen disponible que aún puede acumular el docente en ese rubro.
4. **Debe** informar la fecha de corte o última actualización del cálculo y el ciclo lectivo al que corresponde la valoración.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar a `/puntaje` con un docente que posee antecedentes validados y verificar que la suma de los rubros parciales coincide con el puntaje total exhibido. *(Pasa)*
- **CP-02:** Probar verificar que cada rubro exhibe su puntaje actual frente al tope normativo del Decreto 257/19. *(Pasa)*
- **CP-03:** Probar consultar `/api/puntaje/inscripcion` sin estar autenticado y verificar que el servidor rechaza la petición con HTTP 401. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Alta | Parametrización del baremo del Decreto 257/19, topes por rubro y cruce con registros de Oracle Lab y legajo. |
| **Esfuerzo** | Medio | Motor de cálculo en el backend (`puntaje.service.ts`), fixture normativo y vista desglosada en Angular. |
| **Incertidumbre** | Baja | Reglas de puntuación documentadas en el Decreto 257/19 y ADR-027. |

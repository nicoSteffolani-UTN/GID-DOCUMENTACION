# [ID: US-08] Consulta y cálculo de antigüedad docente desde registros provinciales (PeopleNet)

- **Épica:** E02 - Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)
- **Prioridad:** P2 (Media)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** visualizar en mi perfil mi antigüedad docente total y desglosada reconocida en los sistemas provinciales de Recursos Humanos,  
> **para** conocer los años, meses y días de servicio que se computan para mi valoración reglamentaria según el Decreto 257/19.

## Reglas de Negocio y Conexiones Externas
- **Integración Externa:** Consulta de solo lectura sobre las vistas del esquema `PEOPLENET` en la base de datos Oracle (`PEOPLENET.M4CBA_VW_CONC_DIR` y `PEOPLENET.M4CBA_VW_CONSE_CAR`), según ADR-030.
- **Normativa:** Cómputo de antigüedad docente aplicable al cálculo de puntaje del Decreto Provincial Nº 257/19.

## Criterios de Aceptación
1. **Debe** recuperar los registros de antigüedad y servicios del docente desde las vistas oficiales de `PEOPLENET` en Oracle (o desde el fixture `antiguedad.mock.json` cuando se opere en modo simulado).
2. **Debe** mostrar de forma clara la antigüedad total acumulada expresada en años (y detalle de períodos reconocidos).
3. **Debe** distinguir la antigüedad frente a aula / en el nivel respecto de otros desempeños cuando la fuente de datos lo discrimine.
4. **Debe** informar de manera transparente al usuario cuando el docente sea ingresante y aún no registre antigüedad computada (0 años), sin producir errores de pantalla.
5. **Debe** alimentar con este dato de antigüedad las herramientas de estimación de puntaje y el simulador de carrera docente del portal.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar consultar el perfil con un docente que registra trayectoria en `PEOPLENET` / seed de antigüedad y verificar que se muestra el total de años de servicio reconocidos. *(Pasa)*
- **CP-02:** Probar consultar el perfil con un aspirante recién recibido sin servicios previos (0 años de antigüedad) y verificar que se informa «Sin antigüedad registrada (0 años)» correctamente. *(Pasa)*
- **CP-03:** Probar que un usuario no pueda consultar la antigüedad de otro CUIL distinto al de su propio token `JWT`. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Integración y mapeo de vistas de Recursos Humanos (`PEOPLENET`) en Oracle y consolidación de períodos. |
| **Esfuerzo** | Medio | Adaptador de lectura Oracle + fallback mock y visualización en el componente de perfil. |
| **Incertidumbre** | Media | Estructura de las vistas `M4CBA_VW_CONC_DIR` y `M4CBA_VW_CONSE_CAR` en el ambiente de laboratorio. |

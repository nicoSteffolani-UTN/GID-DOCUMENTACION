# [ID: US-67] ~~Consulta de historial de inscripciones, postulaciones y resultados de LOM de ciclos anteriores~~ [DESESTIMADA - FUERA DE ALCANCE DEMO]

- **Épica:** E13 - Gestión de Reclamos, Historial LOM y Analítica
- **Prioridad:** P3 (Desestimada en demo interactiva)
- **Estimación:** 3 SP (0 SP en MVP final)
- **Rol / Actor:** Docente Aspirante
- **Estado de Alcance:** **Desestimada / Excluida de la demo interactiva** (Decisión de backend y arquitectura 11–13/11).

## Justificación Técnica de la Exclusión
1. **Restricción de datos históricos en laboratorio:** Los registros de inscripciones históricas del laboratorio ministerial MT4D (`T_DETALLES_INSC_JD`) abarcan únicamente hasta el año 2019.
2. **Ausencia de datos en docentes de prueba:** Las cuentas docentes de prueba autorizadas para homologación y demos académicas no poseen registros de postulaciones en dichos ciclos anteriores.
3. **Modo demo offline sin VPN (modo foto):** Para las instancias de demostración desconectadas de la red provincial, el resguardo de privacidad impide almacenar o exponer datos filiatorios ni series históricas individualizadas por docente.
4. **Ambigüedad en esquema de puntajes:** Falta confirmación formal de la DBA ministerial respecto a si el orden de mérito consolidado histórico se rige por la columna `PUNTAJE` o `PUNTAJE_CON`.
Por estos motivos, la trazabilidad de puntajes y méritos del aspirante se concentra en la convocatoria vigente y en la LOM pública de cada establecimiento (`ADR-037`).

## Descripción (Narrativa Original)
> **Como** docente aspirante,  
> **quiero** consultar mi historial de inscripciones de Formulario C y mis resultados definitivos de LOM de ciclos lectivos anteriores,  
> **para** llevar un seguimiento de la evolución plurianual de mi puntaje y descargar mis comprobantes históricos cuando los necesite.

## Reglas de Negocio y Conexiones Externas
- **Alcance Funcional (Instancia 2 §19.13 - US-15):** Consulta histórica de solo lectura agrupada por año/ciclo lectivo (`T_DETALLES_INSC_JD`). Desestimada en la demo interactiva.

## Criterios de Aceptación (Especificación Original)
1. **Debe** listar los registros históricos del docente agrupados por ciclo lectivo (en orden descendente desde el más reciente).
2. **Debe** detallar para cada ciclo lectivo anterior: las escuelas y asignaturas inscriptas, el puntaje definitivo alcanzado y la posición en el orden de mérito si estuviera registrada.
3. **Debe** permitir descargar en formato PDF los comprobantes de inscripciones confirmadas en el portal de períodos previos.
4. **Debe** presentar toda la información histórica en modo exclusivo de solo lectura.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** [No aplicable en demo - Funcionalidad desestimada por restricciones de datos históricos en el laboratorio].
- **CP-02:** Probar consultar el portal en modo demo y verificar que no se exponen opciones rotas o datos desactualizados anteriores a 2019. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Consulta filtrada por CUIL y año/ciclo lectivo sobre tablas de inscripciones. |
| **Esfuerzo** | Bajo | Desestimada del alcance operativo por restricciones de padrón en el lab. |
| **Incertidumbre** | Alta | Ambigüedad en esquema histórico (`PUNTAJE` vs. `PUNTAJE_CON`). |

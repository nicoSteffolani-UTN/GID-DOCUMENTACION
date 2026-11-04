# [ID: US-20] Estimación automática de puntaje según Dto. 257/19 cuando el aspirante aún no registra valoración oficial en Junta

- **Épica:** E04 - Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que aún no posee una LOM histórica liquidada en la base de la Junta o que tiene antecedentes recién cargados,  
> **quiero** que el sistema calcule y me muestre un puntaje estimado automático basado en mi legajo y en el Decreto 257/19, identificándolo claramente como estimación preliminar,  
> **para** poder utilizar igualmente el semáforo del mapa, las ayudas de decisión y el simulador sin quedar bloqueado por falta de historial previo.

## Reglas de Negocio y Conexiones Externas
- **Regla de Transparencia:** Cuando el laboratorio Oracle no devuelve una valoración oficial cerrada para el CUIL o existen antecedentes en estado `Pendiente`, el sistema calcula el puntaje estimado aplicando el Decreto 257/19 sobre el legajo actual e informa explícitamente su carácter no definitivo (ADR-018, ADR-027).

## Criterios de Aceptación
1. **Debe** calcular de forma automática el puntaje estimado conforme al Decreto 257/19 a partir de los títulos, antigüedad (`PEOPLENET`) y antecedentes del legajo cuando el docente no posea registro liquidado en `T_DETALLES_INSC_JD`.
2. **Debe** mostrar de forma diferenciada el **Puntaje ValidadoActual** (solo ítems validados) y el **Puntaje Proyectado con Pendientes** (sumando lo que está en espera de revisión del vocal).
3. **Debe** incluir una leyenda visible que aclare cuando el valor exhibido corresponde a una **estimación reglamentaria preliminar (Dto. 257/19)** sujeta al dictamen definitivo de la Junta de Clasificación.
4. **Debe** permitir que este puntaje estimado alimente el Semáforo LOM del mapa territorial y las recomendaciones de escuelas del Formulario C.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar ingresar con un docente sin puntaje histórico en Oracle pero con título y antecedentes en su legajo y verificar que el sistema calcula y muestra su puntaje estimado según el Decreto 257/19 con el aviso de estimación preliminar. *(Pasa)*
- **CP-02:** Probar agregar un nuevo curso en estado `Pendiente` al legajo y verificar que en la vista de puntaje se distingue el puntaje firme actual del puntaje potencial si dicho curso es aprobado. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Fallback automático entre lectura de valoración histórica en Oracle y cálculo en vivo sobre el legajo del portal. |
| **Esfuerzo** | Medio | Integración entre los servicios de `legajo`, `perfil` (antigüedad) y `puntaje`. |
| **Incertidumbre** | Baja | Comportamiento definido y documentado en ADR-027 y `DATOS_REALES_Y_SIMULADOS.md`. |

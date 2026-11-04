# ADR-022: Asistente docente (chatbot) propio del portal

**Estado:** Aceptado  
**Fecha:** 2026-09-28  
**Actualizado:** 2026-09-28 (modo Oracle lab)  
**Complementado por:** ADR-023 (modelo de lenguaje como intérprete del texto libre)  
**Relacionado:** ADR-004 (monolito modular), ADR-005 (JWT), ADR-009 (alcance de título), ADR-010 (motor LOM), ADR-011 (simulador), ADR-012 (capacitaciones), ADR-019 (semáforo LOM del mapa)

## Contexto

El docente tiene que cruzar información de varias pantallas para decidir dónde inscribirse: qué materias le habilitan sus títulos, qué escuelas tienen vacantes cerca, cómo está su puntaje frente al promedio de cada escuela y qué cursos le suman puntos. La propuesta del equipo es un asistente conversacional con estos pedidos (intents):

| Intent | Qué resuelve |
|--------|--------------|
| `recomendar_materias` | Materias y cargos que habilitan sus títulos: primero como docente, luego como supletorio |
| `recomendar_escuelas_ubicacion` | Las 5 escuelas que mejor combinan cercanía y promedio LOM en una zona |
| `evaluar_postulacion_escuela` | «¿Me conviene?»: su puntaje frente al promedio de una escuela y las materias con vacantes que puede tomar |
| `recomendar_cursos` | Cursos del catálogo filtrados por tema o modalidad |
| `cargar_titulo` | Lleva al legajo con el título elegido del catálogo |
| `postular_escuela_especifica` | Lleva al Formulario C con la escuela y la materia elegidas, para confirmar |
| `simular_trayectoria` | Escenarios del simulador de puntaje |
| `plan_escuela` | Cuánto le falta para el promedio de una escuela y qué cursos lo cubren con menos horas |
| `proximo_paso` | Qué le conviene hacer ahora: los cursos que más escuelas le abren (no los que más puntos dan) o, si ninguno abre una por sí solo, la escuela más cercana; lleva al simulador de carrera con el curso ya puesto (ADR-024) |
| `revisar_pendientes` | Todo lo que tiene para atender (rechazos, inscripción, revisiones pendientes, vacantes de interés) |

Además, el asistente **avisa sin que le pregunten**: el botón muestra un contador de avisos nuevos y, al abrirse, el saludo incluye los más importantes.

El boceto inicial proponía Botpress. Antes de implementarlo se evaluaron las opciones con estas restricciones: los datos del laboratorio Oracle solo son accesibles desde la red del proyecto, la API corre en local o en Docker (ADR-021) y la demo ante la cátedra tiene que ser confiable.

## Decisión

Implementar un **asistente propio dentro del portal**:

- **Backend** (`src/server/src/modules/asistente/`): módulo con `GET /api/asistente/inicio` y `POST /api/asistente/mensaje`, solo para el rol `aspirante`. Usa los mismos servicios que las pantallas (alcance de título, mapa LOM, puntaje, capacitaciones, simulador, Formulario C), así que no duplica reglas de negocio.
- **Reconocimiento de lenguaje:** intents por palabras clave con prioridad fija y entidades buscadas en los catálogos del sistema (escuelas por nombre, sigla y número o código; localidades y departamentos con tolerancia a errores de tipeo; materias; títulos). Cuando falta un dato, el asistente lo pide y lo valida, y sugiere alternativas si no lo reconoce.
- **Sin estado en el servidor:** el contexto de la conversación viaja en cada mensaje y el servidor solo toma sus campos conocidos. La conversación se guarda en `sessionStorage` de la pestaña y se borra al cerrar sesión.
- **Frontend:** botón flotante y panel en el layout, solo para docentes. Las respuestas combinan texto, listas con semáforo y opciones; las que llevan a una pantalla aplican filtros por URL (`/mapa`, `/capacitaciones`, `/formulario-c`, `/legajo`).
- **Nada se guarda sin confirmación del docente:** el asistente precarga el Formulario C o el legajo, pero la inscripción o el alta se confirman en esas pantallas.

### Avisos proactivos

`GET /api/asistente/avisos` devuelve lo que el docente tiene para atender, ordenado por prioridad:

| Prioridad | Aviso |
|-----------|-------|
| Alta | Antecedente rechazado por la junta, con el motivo |
| Alta | Inscripción abierta sin escuelas elegidas (si tiene títulos) |
| Alta | Materia de la inscripción que sus títulos ya no habilitan |
| Media | Título sin cargar |
| Media | Escuela de la inscripción sin materias ni cargos |
| Media / baja | Vacante en una escuela y materia que marcó como interés en el mapa (media si la cobertura es urgente) |
| Baja | Antecedentes que esperan la revisión de la junta |

Los avisos de inscripción solo aparecen con la inscripción abierta. Cada aviso tiene un identificador estable: el cliente guarda en la pestaña cuáles ya vio y el contador del botón cuenta solo los de prioridad alta o media sin ver. El sistema no registra la fecha de publicación de las vacantes, así que el aviso dice que la escuela «tiene vacantes», no que son nuevas.

### Plan para llegar a una escuela

- **Diferencia:** promedio LOM de la escuela menos el puntaje del docente (el mismo que usa «¿me conviene?»). Si no hay diferencia, lo dice y ofrece anotarse.
- **Cursos:** del catálogo, sin los que ya figuran en el legajo, se eligen los que suman al menos la diferencia pasándose lo menos posible (mochila 0/1); como los puntos son proporcionales a las horas (Dto. 257/19: 0,017 por hora, cursos de 20 h o más), también son las menos horas. A igual puntaje, menos cursos.
- **Si el catálogo no alcanza:** muestra los cursos que más suman y estima el resto con la antigüedad según el Dto. 257/19 (1 punto por año frente a alumnos, tope 10; ADR-027). Con el puntaje estimado se compara con lo que le queda del tope. La valoración LOM oficial informa la antigüedad en su propia escala y no dice cuánto del tope ya usó, así que se toma el tope completo como máximo: si lo que falta lo supera, no se cubre con años de servicio; si no, se aclara que vale mientras no haya llegado al tope. Cuando los años no alcanzan, sugiere probar otro título en el simulador.
- **Ediciones de un mismo curso:** dos ofertas del catálogo con el mismo nombre son el mismo curso y cuentan una sola vez (la que más suma), con el mismo criterio que se usa para no sugerir cursos ya hechos.
- Es una estimación: los puntos cuentan cuando el curso se acredita y la junta lo acepta, y el promedio de la escuela es histórico.

### Reglas de datos

- **Puntaje para comparar:** la valoración LOM oficial (el mayor total, igual que el semáforo del mapa); si el docente no la tiene, el estimado de «Mi puntaje» con los antecedentes aceptados por la junta, aclarado en la respuesta.
- **Promedio de la escuela:** el promedio LOM histórico es **por escuela**; el sistema no tiene promedios por materia y el asistente lo aclara.
- **Semáforo:** mismas reglas que el mapa: verde si el promedio no supera el puntaje, amarillo hasta 10 puntos por encima, rojo si lo supera por más, azul sin datos.
- **Ranking de escuelas:** `PUNTAJE = 0,6 · distancia normalizada + 0,4 · promedio normalizado`, con `(Dmax − Di) / (Dmax − Dmin)` y `(Pmax − Pi) / (Pmax − Pmin)` (menos distancia y menos promedio suman más). Si el rango es menor a 1 km o a 1 punto, se usa ese mínimo para que diferencias de metros o décimas no decidan el orden. Entran las escuelas de la zona con vacantes en materias que habilitan sus títulos; si no hay, las de la zona. Con la ubicación del navegador, las que están a menos de 25 km (o las 10 más cercanas si son menos de 5). Con una localidad o departamento, la distancia se mide desde el centro de sus escuelas.

### Modo Oracle lab (`INTEGRATION_MODE=prod`)

El asistente toma sus datos del mismo contenedor de integraciones que el resto del portal, así que en `prod` usa
alcance, padrón, mapa y LOM del lab sin configuración propia. Al probarlo contra el lab aparecieron diferencias con el
mock que se resolvieron así:

- **Escuelas sin catálogo:** unas 110 de las ~1.165 escuelas del padrón no tienen materias en `VT_MATCAR_EMPRESA` y el
  adaptador lo informa con un error. El asistente las toma como escuelas sin materias cargadas en lugar de fallar.
- **Rendimiento:** con ~73 mil filas de alcance, evaluar la habilitación materia por materia tardaba ~4 s por respuesta.
  `ValidadorService.evaluarHabilitaciones` evalúa varias materias recorriendo el alcance una sola vez (mismo criterio
  que la evaluación individual) y cada título se normaliza una sola vez; las respuestas bajaron a decenas de ms.
- **Materias habilitadas:** el alcance del lab incluye códigos de otros niveles que no aparecen en ninguna escuela ni
  vacante. El asistente lista solo materias que existen en el sistema (catálogo del padrón o vacantes del mapa) y con
  nombre, une las que comparten nombre, muestra primero las que tienen vacantes publicadas en más escuelas del mapa y
  corta en 12, con la opción «Ver todas».
- **Datos que siguen siendo de referencia:** las vacantes del lab son publicaciones históricas (ADR-016, ADR-019); el
  CUIL demo no tiene valoración LOM en el lab y usa el respaldo demo del portal, que el asistente muestra como su
  puntaje LOM, igual que el semáforo del mapa. Simulador y cursos siguen en mock.

## Alternativas consideradas

| Alternativa | Motivo para descartarla |
|-------------|------------------------|
| **Botpress v12 (self-hosted)** | Encaja con el diseño (flujos visuales, NLU en español, webchat), pero el proveedor lo discontinuó: su documentación indica que ya no está disponible para descargas ni para nuevos despliegues, y el NLU depende de modelos de lenguaje que se descargan de servidores de Botpress. Sumar una herramienta sin soporte ni parches de seguridad no es defendible. |
| **Botpress Cloud** | Plataforma vigente, pero exige que la API del portal sea accesible desde internet (túnel o despliegue público), enviar la identidad del docente a un servicio externo y depender de internet durante la demo. La lógica del bot quedaría fuera del repositorio. |
| **Asistente propio + modelo de lenguaje (LLM)** | Entendería mejor el texto libre, pero requiere clave de API, conexión a internet y costo por uso, y enviaría datos del docente a un tercero. Las respuestas serían menos predecibles para la demo. |

La lógica de datos quedó en el backend como API propia: si más adelante se adopta una plataforma de chatbot o un LLM, puede usar estos mismos endpoints sin reescribir las reglas.

## Consecuencias

**Positivas**

- Todo queda en el repositorio, con tests (reconocimiento de lenguaje, ranking y conversaciones completas contra la API) y funciona sin internet.
- Usa la sesión y los roles existentes (JWT, ADR-005); no hay datos personales en servicios externos ni conversaciones guardadas en el servidor.
- Reconoce nombres propios del dominio (escuelas, localidades, títulos) con los catálogos reales, mejor que un NLU genérico.

**Negativas / limitaciones**

- El texto libre se entiende por palabras clave: frases muy distintas de las previstas caen en «no entendí» y el asistente vuelve a ofrecer las opciones.
- En modo Oracle lab el alcance de título no trae la condición (docente o supletorio): todas las materias figuran como habilitadas como docente (ADR-009).
- El simulador y el recomendador de capacitaciones siguen usando datos de prueba (ADR-011, ADR-012); el asistente muestra el aviso correspondiente.
- El promedio por escuela no distingue materias: la comparación es orientativa.

## Referencias

- Código: `src/server/src/modules/asistente/`, `src/web/src/app/features/asistente/`, `src/web/src/app/core/asistente/`
- API: [`docs/api/openapi.yaml`](../api/openapi.yaml) — tag Asistente
- Botpress: [Botpress v12 (and self-hosted versions)](https://botpress.com/docs/studio/guides/advanced/v12/)

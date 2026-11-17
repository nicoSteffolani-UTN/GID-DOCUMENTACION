# ADR-023: Asistente docente con modelo de lenguaje (Groq)

**Estado:** Aceptado  
**Fecha:** 2026-10-01  
**Relacionado:** ADR-022 (asistente propio), ADR-005 (JWT)

## Contexto

El asistente del ADR-022 entiende el texto libre por palabras clave: lo que no se parece a las frases previstas cae en «no entendí», no responde preguntas generales ni sigue el hilo de una charla. El ADR-022 descartó un LLM por tres motivos: enviaba datos del docente a un tercero, dependía de internet y las respuestas eran menos predecibles. El uso real indica que casi todas las consultas serán texto libre, así que el modelo pasa a ser la vía principal.

## Decisión

Sumar un **modelo de lenguaje como intérprete** del texto libre, sin cambiar dónde se calculan los datos:

- **Proveedor:** Groq (API compatible con OpenAI), modelo `openai/gpt-oss-120b` por defecto (`GROQ_MODEL`). Se accede con `fetch`, sin SDK, detrás de la interfaz `InterpretePort`: cambiar de proveedor es escribir otro adaptador.
- **El modelo solo clasifica y nunca redacta lo que ve el docente.** Para cada mensaje debe elegir una de cuatro funciones (`tool_choice: required`, no puede responder con texto libre): `consultar` (una de las 15 consultas del portal, con las entidades que mencionó), `explicar` (un tema de una lista fija: LOM, semáforo, habilitante/supletorio, Formulario C, intereses, legajo), `rechazar` (fuera de alcance, sin información o pide modificar) o `aclarar` (una pregunta corta). Los textos de `explicar` y `rechazar` son plantillas del backend (`asistente.explicaciones.ts`). Una prueba con redacción libre del modelo mostró que inventaba pantallas y fechas («Calendario de Inscripción», «sección de seguridad»), por eso se descartó.
- **Consultas del perfil de solo lectura:** puntaje, legajo, inscripción, intereses y vacantes (todas las abiertas, marcando las de sus intereses; solo las propias si lo pide). El modelo elige de una lista cerrada: no escribe SQL ni puede modificar nada, y un test comprueba que ninguna consulta cambia los datos del docente.
- **Agente de consultas para lo que no tiene consulta fija (`investigar`).** Las preguntas de datos del portal que ninguna de las 14 consultas resuelve exacto (por ejemplo «¿qué escuelas de Villa Allende tienen el promedio más bajo?» o «¿qué título tengo y qué materias me habilita?») las responde un agente con **herramientas de consulta parametrizadas y de solo lectura**: `buscar_escuelas`, `buscar_vacantes`, `buscar_materias`, `buscar_cursos`, `mi_perfil` y `comparar_escuela`. El modelo elige filtros, no escribe SQL; el backend valida y acota los argumentos (límite de 15 resultados) y ejecuta la búsqueda sobre los mismos datos de las pantallas. Ninguna herramienta modifica datos ni devuelve datos de otros docentes (un test lo verifica).
- **Verificación de la respuesta del agente.** Es la única parte donde el modelo redacta texto libre, así que toda cifra de la respuesta debe aparecer en los datos que recibió (o en la pregunta); si no, se descarta y el asistente avisa que no pudo armar una respuesta confiable. En la primera ronda el agente está obligado a buscar datos: no puede contestar de memoria.
- **Datos personales hacia el modelo, acotados.** `mi_perfil` y `comparar_escuela` envían puntaje, nombres de títulos, estado de antecedentes, inscripción e intereses; **nunca CUIL ni nombre**. `LLM_ENVIAR_PERFIL=false` los apaga y el modelo no ve nada personal (esto debe confirmarse con la cátedra).
- **Los datos los sigue calculando el backend.** La consulta pasa por el mismo motor del ADR-022: las entidades se validan contra los catálogos y el ranking, el puntaje y las listas salen del código. El clasificador nunca ve CUIL, nombre, títulos ni puntajes; el agente solo ve el resumen acotado descrito arriba.
- **Qué va al modelo:** el prompt, los últimos 6 turnos de texto plano de la conversación y el mensaje del docente. Los botones y el dato que el asistente acaba de pedir (zona, escuela, materia) siguen resolviéndose por las reglas, sin modelo.
- **Respaldo:** sin `GROQ_API_KEY`, en test, o ante error, tiempo agotado o límite de uso (429), el asistente responde con las reglas. Ante un 429 se deja de llamar al modelo durante el `retry-after` indicado.
- **Entrada no confiable:** lo que devuelve el modelo se valida igual que el texto del docente (función, intent, tema y motivo de listas cerradas; campos acotados). Las herramientas son de solo lectura y nada se guarda sin que el docente lo confirme en la pantalla correspondiente.
- **Secreto:** la clave va solo en `src/.env` (ignorado por Git) o en variables de entorno; `.env.example` lleva el nombre vacío.

## Alternativas consideradas

| Alternativa | Motivo |
|-------------|--------|
| Solo reglas (ADR-022) | No cubre el texto libre que usará la gente. Queda como respaldo. |
| Entrenar o afinar un modelo propio | No hay un conjunto de conversaciones y no aporta frente a un modelo general con herramientas. |
| Agente que ve los datos y redacta todo | Mejor redacción, pero expone datos personales a un tercero y permite cifras inventadas. |
| Otros proveedores (Gemini, OpenRouter, Mistral, modelos de pago) | Siguen siendo viables: la interfaz `InterpretePort` permite cambiarlos sin tocar el resto. |

## Consecuencias

**Positivas**

- Entiende frases informales y preguntas compuestas, y las preguntas generales se contestan con textos fijos revisados por el equipo.
- Los datos personales no salen del servidor y las respuestas con cifras siguen siendo deterministas y probadas.
- Si el modelo falla, el asistente sigue funcionando.

**Negativas / limitaciones**

- El texto que escribe el docente y los últimos turnos de la conversación salen a un tercero: hay que informarlo y confirmarlo con la cátedra antes de producción.
- Depende de internet y de los límites del plan gratuito de Groq (tokens por minuto): con varios usuarios a la vez algunos mensajes caerán en las reglas.
- El modelo puede confundir el intent en frases ambiguas; las entidades se validan, pero el intent no. Se mejora ajustando el prompt y con una batería de frases.
- Las preguntas conceptuales se limitan a los temas de la lista (se amplía con plantillas); las de datos las cubre el agente, que puede equivocarse en la interpretación aunque no en las cifras.
- Una pregunta de datos usa entre 2 y 4 llamadas al modelo: con el plan gratuito de Groq el límite por minuto se agota con pocos usuarios simultáneos.

## Addendum (07/11/2026): preguntas fijas, trayectoria y reclamos

- **Preguntas frecuentes sin modelo:** las explicaciones solo llegaban si el modelo las elegía, así que sin clave de Groq no existían. Ahora hay un intent `explicar`: un botón o una regla piden el tema y el texto es siempre el mismo. El modelo sigue resolviendo lo que se escribe distinto.
- **Temas nuevos:** puntaje estimado, reclamos, reportes y cursos. Se sacan alcance de título e intereses.
- **Consultas nuevas:** `consultar_trayectoria` (la constancia de Mi perfil) y `consultar_reclamos`. La trayectoria es la única respuesta asíncrona de las reglas, porque consulta al lab: pasa por `responderConLlm` y, si el lab no responde, lo avisa.
- **Menú:** las opciones traen `grupo` y después de una respuesta se ofrecen pocas, con «Ver todas las opciones».
- **Se saca:** «Mis intereses», el filtro de vacantes por intereses y el aviso de cada escuela marcada; también la competencia en las materias y el aviso de materias que los títulos no habilitan. Los intereses siguen en Alertas de vacantes y en el mapa.
- **Aviso nuevo:** cuando la Junta resuelve un reclamo.
- **Nombre:** el saludo usa el nombre de pila aunque CiDi lo mande en mayúsculas o con el apellido primero.

## Addendum (15/11/2026): modelos de respaldo, contexto y carga guiada

- **Modelos de respaldo:** `GROQ_MODEL` acepta una lista separada por comas, en orden de preferencia (por defecto `openai/gpt-oss-120b,openai/gpt-oss-20b,llama-3.3-70b-versatile`). Si uno responde 429 por cuota, el asistente pasa al siguiente y no vuelve a insistir con el agotado hasta que pase el tiempo indicado. Atenúa la limitación del plan gratuito de Groq.
- **Contexto del docente:** el agente conoce lo que el docente ya hizo (legajo, inscripción, puntaje) y explica cómo funciona cada pantalla con textos fijos; sigue sin recibir CUIL ni nombre.
- **Carga guiada de antecedentes (`cargar_antecedente`):** el asistente pregunta dato por dato con las mismas validaciones que el formulario, deja vacíos los opcionales y se puede cancelar. No guarda nada: al terminar abre el legajo con el formulario precargado (`navegar.estado`, sin pasar los datos por la URL). El servidor descarta los valores que no son de la categoría aunque los mande el cliente.

## Referencias

- Código: `src/server/src/modules/asistente/asistente.llm.ts`, `asistente.service.ts` (`responderConLlm`), `asistente.carga.ts`
- Configuración: `GROQ_API_KEY`, `GROQ_MODEL` (`src/.env.example`)

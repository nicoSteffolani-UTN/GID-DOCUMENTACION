# Matriz General de Casos de Prueba por Historia de Usuario (US-01 a US-80)

Esta matriz unifica los **Casos de Prueba de Aceptación de Usuario (`CP-01`..`CP-04`)** definidos dentro de cada una de las **80 Historias de Usuario** con los **Casos de Prueba Automatizados (`CP-P1-XX`, `CP-P2-XX` y `CP-WEB-XX`)** de las suites de Backend (`Jest` en [`casos-prueba-sprint-p1.md`](../PF-24---Gestion-Docente-Secundaria/docs/casos-prueba-sprint-p1.md) y [`casos-prueba-sprint-p2.md`](../PF-24---Gestion-Docente-Secundaria/docs/casos-prueba-sprint-p2.md)) y Frontend (`Karma/Jasmine`).

---

## E01 — Identidad Digital, Autenticación y Sesión (CiDi)

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-01](./US-01-ingreso-redireccion-cidi.md)** | Ingreso por redirección desde Ciudadano Digital (CiDi) | `CP-01` (Ingreso válido CiDi → sesión activa), `CP-02` (Ruta privada sin sesión → 401), `CP-03` (Token JWT alterado → rechazado) | `CP-P1-01`, `CP-P1-02`, `CP-P2-45`, `CP-WEB-15` | `cidi.test.ts`, `app.test.ts`, `auth.service.spec.ts` |
| **[US-02](./US-02-verificacion-cidi-nivel-2.md)** | Verificación obligatoria de Identidad CiDi Nivel 2 | `CP-01` (Cuenta Nivel 2 → acceso permitido), `CP-02` (Cuenta Nivel 1 `20987654321` → bloqueada con guía), `CP-03` (API con Nivel 1 → 403) | `CP-P1-02`, `CP-P2-45` | `cidi.test.ts`, `app.test.ts` |
| **[US-03](./US-03-bifurcacion-entorno-rol.md)** | Bifurcación automática de entorno según rol (`aspirante` / `vocal`) | `CP-01` (Aspirante → `/home`), `CP-02` (Vocal → `/junta`), `CP-03` (Aspirante en `/junta` → 403), `CP-04` (API cruzada de rol → 403) | `CP-P1-01`, `CP-P2-21`, `CP-P2-34`, `CP-P2-45` | `cidi.test.ts`, `junta.test.ts`, `carrera.test.ts`, `decisiones.test.ts` |
| **[US-04](./US-04-expiracion-inactividad-cierre-sesion.md)** | Expiración por inactividad (30 min) y cierre seguro de sesión | `CP-01` (Cerrar sesión manual → limpia token), `CP-02` (Inactividad > 30 min → caduca sesión), `CP-03` (Botón Atrás post-logout → bloqueado) | `CP-WEB-13`, `CP-WEB-15` | `auth.service.spec.ts`, `simulador-sesion.spec.ts` |
| **[US-05](./US-05-arnes-ingreso-simulado-temporal.md)** | Arnés de ingreso simulado temporal para desarrollo y homologación | `CP-01` (Login demo `20123456789` → JWT idéntico a CiDi), `CP-02` (Cambio entre vocales demo), `CP-03` (Credencial inexistente → 401) | `CP-P1-01`, `CP-P1-02`, `CP-P2-45` | `app.test.ts`, `cidi.test.ts`, `demo-flow.test.ts` |

---

## E02 — Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-06](./US-06-panel-inicio-estado-ciclo.md)** | Panel de inicio con estado actual del ciclo administrativo docente | `CP-01` (Resumen de indicadores activos en `/home`), `CP-02` (Indicador de ventana cerrada), `CP-03` (Navegación rápida a módulos) | `CP-P1-12`, `CP-WEB-03` | `mvp-chain.test.ts`, `formulario-c.service.spec.ts` |
| **[US-07](./US-07-consulta-datos-personales-perfil.md)** | Consulta de datos personales e institucionales en Mi Perfil | `CP-01` (Visualización de CUIL, nombre y títulos), `CP-02` (Campos filiatorios en solo lectura, sin escuela base ADR-013), `CP-03` (Sin sesión → 401) | `CP-P2-46`, `CP-WEB-19` | `perfil.test.ts`, `perfil.service.spec.ts` |
| **[US-08](./US-08-consulta-calculo-antiguedad-peoplenet.md)** | Consulta y cálculo de antigüedad docente desde `PEOPLENET` | `CP-01` (Cómputo de años/meses desde `PEOPLENET`), `CP-02` (Docente ingresante con 0 años), `CP-03` (Aislamiento por CUIL del token) | `CP-P2-35`, `CP-P2-46` | `oracle-antiguedad.adapter.test.ts`, `perfil.test.ts` |
| **[US-09](./US-09-visualizacion-cargos-vigentes-trayectoria.md)** | Visualización de cargos vigentes e historial de trayectoria activa | `CP-01` (Listado de cargos vigentes y situación de revista), `CP-02` (Estado vacío sin cargos activos), `CP-03` (Orden: vigentes primero, anteriores después) | `CP-P2-46` | `oracle-antiguedad.adapter.test.ts`, `perfil.test.ts` |
| **[US-10](./US-10-configuracion-ubicacion-radio-traslado.md)** | Configuración de ubicación de referencia y radio de traslado | `CP-01` (Guardar radio de viaje en km), `CP-02` (Rechazo de radio negativo o inválido), `CP-03` (Impacto en sugerencias por cercanía) | `CP-P2-28`, `CP-WEB-10` | `zona-util.spec.ts`, `formulario-c-estrategia.test.ts` |

---

## E03 — Gestión del Legajo Digital y Antecedentes

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-11](./US-11-carga-manual-antecedentes-pdf.md)** | Carga manual de títulos y antecedentes externos con PDF probatorio | `CP-01` (Alta válida con PDF ≤ 1 MB → estado `Pendiente`), `CP-02` (Formato distinto de PDF → rechazado), `CP-03` (Archivo > 1 MB → rechazado), `CP-04` (Fecha futura → rechazado) | `CP-P1-03`, `CP-P1-04`, `CP-WEB-04`, `CP-WEB-16` | `mvp-chain.test.ts`, `legajo-faltantes.spec.ts`, `archivo-adjunto.util.spec.ts` |
| **[US-12](./US-12-sincronizacion-automatica-antecedentes-sge.md)** | Sincronización automática de antecedentes desde SGE / Provincia | `CP-01` (Primera sincronización importa registros provinciales), `CP-02` (Segunda sincronización idempotente sin duplicar), `CP-03` (CUIL sin antecedentes externos) | `CP-P1-05`, `CP-P2-47` | `mvp-chain.test.ts`, `oracle-sge.adapter.test.ts` |
| **[US-13](./US-13-consulta-listado-estados-legajo.md)** | Consulta del listado del legajo y seguimiento de estados | `CP-01` (Visualización de ítems `Pendiente`, `Validado`, `Rechazado`), `CP-02` (Filtro por tipo/estado), `CP-03` (Estado vacío inicial) | `CP-P1-03`, `CP-P2-17` | `mvp-chain.test.ts`, `puntaje.test.ts` |
| **[US-14](./US-14-previsualizacion-descarga-certificado-pdf.md)** | Previsualización y descarga del certificado PDF adjunto | `CP-01` (Apertura de PDF propio en navegador), `CP-02` (Visualización por el vocal en revisión), `CP-03` (Bloqueo de acceso a PDF ajeno) | `CP-WEB-16` | `archivo-adjunto.util.spec.ts`, `junta-antecedentes.spec.ts` |
| **[US-15](./US-15-baja-antecedentes-manuales-pendientes.md)** | Baja o eliminación de antecedentes manuales en estado `Pendiente` | `CP-01` (Eliminación exitosa de manual pendiente), `CP-02` (Bloqueo de borrado de origen provincial/validado o de otro CUIL), `CP-03` (Cancelación de diálogo) | `CP-P1-06` | `mvp-chain.test.ts` |
| **[US-16](./US-16-consulta-observaciones-motivos-rechazo.md)** | Consulta detallada de observaciones y motivos de rechazo del Vocal | `CP-01` (Visualización de motivo, fecha y vocal tras rechazo), `CP-02` (Legajo sin observaciones), `CP-03` (Privacidad por CUIL) | `CP-P2-07`, `CP-P2-08` | `demo-flow.test.ts`, `junta.test.ts` |

---

## E04 — Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-17](./US-17-consulta-puntaje-desglose-decreto-257.md)** | Consulta del puntaje total y desglose por rubro (Dto. 257/19) | `CP-01` (Suma de rubros coincide con total), `CP-02` (Exhibición de topes reglamentarios por artículo), `CP-03` (Sin autenticación → 401) | `CP-P2-01`, `CP-P2-17`, `CP-P2-35`, `CP-P2-36` | `puntaje.test.ts`, `decreto-257.test.ts`, `demo-flow.test.ts` |
| **[US-18](./US-18-ponderacion-automatica-capacitaciones-titulos.md)** | Ponderación automática de capacitaciones y títulos (Dto. 257/19) | `CP-01` (Curso ≥ 20 h calcula `horas × 0,017`), `CP-02` (Curso < 20 h figura en «no suma» con `0,00` pts), `CP-03` (Rechazados no suman) | `CP-P2-32`, `CP-P2-35`, `CP-P2-36` | `decreto-257.test.ts`, `puntaje.test.ts`, `decisiones.service.test.ts` |
| **[US-19](./US-19-consulta-puntaje-por-escuela-materia.md)** | Consulta de puntaje discriminado por escuela y materia inscripta | `CP-01` (Diferencia de puntaje según carácter Docente/Habilitante/Supletorio por materia), `CP-02` (Inspección por fila de inscripción) | `CP-P2-01`, `CP-P2-36` | `puntaje.test.ts`, `oracle-lom.adapter.test.ts` |
| **[US-20](./US-20-estimacion-automatica-puntaje-sin-valoracion-previa.md)** | Estimación automática según Dto. 257/19 sin valoración previa en Junta | `CP-01` (Cálculo estimado automático con aviso de carácter preliminar), `CP-02` (Distinción entre validado actual y proyectado con pendientes) | `CP-P2-02`, `CP-P2-16`, `CP-P2-35` | `puntaje.test.ts`, `decreto-257.test.ts` |
| **[US-21](./US-21-distincion-lom-provisoria-definitiva.md)** | Distinción de estado de publicación de LOM (Provisoria vs. Definitiva) | `CP-01` (Indicador de instancia provisoria y plazo de revisión), `CP-02` (Indicador de valoración oficial/definitiva) | `CP-P2-01`, `CP-P2-35` | `puntaje.test.ts`, `oracle-lom.adapter.test.ts` |

---

## E05 — Validador de Alcance de Títulos y Competencias

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-22](./US-22-consulta-catalogo-titulos-busqueda-competencias.md)** | Consulta del catálogo oficial de títulos (74k) y competencias | `CP-01` (Búsqueda de «PROFESOR DE COMPUTACION Y MATEMATICA» → cód. 320 Docente), `CP-02` (Búsqueda parcial sin tildes), `CP-03` (Texto sin coincidencias) | `CP-P1-13`, `CP-WEB-02` | `validador.service.test.ts`, `oracle-alcance.adapter.test.ts`, `parse-titulos-alcances-sql.test.ts`, `validador.service.spec.ts` |
| **[US-23](./US-23-validacion-tiempo-real-titulo-asignatura.md)** | Validación en tiempo real de compatibilidad Título–Asignatura | `CP-01` (Asignatura habilitada devuelve categoría Docente/Habilitante/Supletorio), `CP-02` (Múltiples títulos aplica el mejor alcance) | `CP-P1-10`, `CP-P1-11`, `CP-WEB-02` | `mvp-chain.test.ts`, `validador.service.test.ts`, `validador.service.spec.ts` |
| **[US-24](./US-24-busqueda-inversa-titulos-por-materia.md)** | Búsqueda inversa de títulos habilitantes por espacio curricular | `CP-01` (Consulta por asignatura lista títulos por categoría), `CP-02` (Resalta título propio del legajo) | `CP-P1-13` | `validador.service.test.ts`, `oracle-alcance.adapter.test.ts` |
| **[US-25](./US-25-advertencia-preventiva-materia-sin-alcance.md)** | Advertencia preventiva por selección de asignatura sin alcance | `CP-01` (Seleccionar materia 656 sin alcance muestra advertencia preventiva), `CP-02` (Reemplazar por materia con alcance quita la alerta) | `CP-P1-11`, `CP-P1-14` | `mvp-chain.test.ts` |
| **[US-26](./US-26-marcado-automatico-sin-titulo-habilitante-vocal.md)** | Marcado automático de «Sin título habilitante» para el Vocal | `CP-01` (Confirmar materia sin alcance marca «Sin título habilitante» en bandeja del vocal), `CP-02` (Inscripción 100% habilitada sin alerta) | `CP-P1-14`, `CP-P2-04` | `mvp-chain.test.ts`, `junta.test.ts` |

---

## E06 — Convocatoria Anual e Inscripción en Formulario C

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-27](./US-27-consulta-busqueda-padron-escuelas-oferta-curricular.md)** | Consulta y búsqueda del padrón oficial de escuelas y su oferta | `CP-01` (Búsqueda por sigla/número ej. `ipem 501` o departamento), `CP-02` (Despliegue de catálogo curricular y filtro por especialidad) | `CP-P1-15` | `oracle-padron.adapter.test.ts`, `mvp-chain.test.ts` |
| **[US-28](./US-28-confeccion-formulario-c-tope-3x5.md)** | Confección del Formulario C con tope estricto (3 escuelas × 5 materias) | `CP-01` (Selección válida 3×5), `CP-02` (Bloqueo 4ª escuela → 400), `CP-03` (Bloqueo 6ª materia → 400), `CP-04` (Bloqueo escuela vacía) | `CP-P1-07`, `CP-P1-08`, `CP-P1-09`, `CP-P1-15` | `mvp-chain.test.ts`, `formulario-c.service.spec.ts` |
| **[US-29](./US-29-guardado-borrador-edicion-formulario-c.md)** | Guardado en borrador, edición y alerta al salir sin guardar | `CP-01` (Persistencia entre sesiones), `CP-02` (Edición y guardia `sinGuardarGuard` antes de abandonar pantalla con cambios pendientes) | `CP-WEB-03`, `CP-WEB-17` | `sin-guardar.guard.spec.ts`, `confirm-dialog.component.spec.ts`, `formulario-c.service.spec.ts` |
| **[US-30](./US-30-confirmacion-definitiva-formulario-c.md)** | Confirmación definitiva de la inscripción anual en el Formulario C | `CP-01` (Confirmación exitosa con aviso de revisión de Junta), `CP-02` (Aparición inmediata del aspirante en la escuela del vocal) | `CP-P1-09`, `CP-P2-04`, `CP-P2-37`, `CP-WEB-17` | `mvp-chain.test.ts`, `junta.test.ts`, `demo-flow.test.ts` |
| **[US-31](./US-31-emision-descarga-comprobante-pdf-formulario-c.md)** | Emisión y descarga del comprobante digital PDF sellado | `CP-01` (Descarga de comprobante con CUIL, escuelas, materias y sello temporal), `CP-02` (No disponible sin inscripción confirmada) | `CP-P1-09`, `CP-WEB-03` | `formulario-c.service.spec.ts`, `mvp-chain.test.ts` |
| **[US-32](./US-32-bloqueo-modificacion-fuera-ventana-convocatoria.md)** | Bloqueo de alta y modificación fuera de la ventana de convocatoria | `CP-01` (`INSCRIPCION_ABIERTA=false` bloquea edición en UI), `CP-02` (Petición PUT/POST directa a API devuelve 400/403) | `CP-P1-12`, `CP-WEB-03` | `mvp-chain.test.ts`, `formulario-c.service.spec.ts` |

---

## E07 — Ayudas a la Decisión y Recomendación de Postulación

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-33](./US-33-propuesta-asistida-formulario-c-segura-justa-apuesta.md)** | Propuesta asistida de Formulario C (*Escuela Segura*, *Justa* y *Apuesta*) | `CP-01` (Propuesta de hasta 3 escuelas clasificadas con 1 a 5 materias y motivos), `CP-02` (Sin título aceptado devuelve 422 explicativo) | `CP-P2-27`, `CP-P2-29`, `CP-P2-34` | `formulario-c-estrategia.test.ts`, `formulario-c.estrategia.test.ts` |
| **[US-34](./US-34-revision-inteligente-previa-confirmacion-formulario-c.md)** | Revisión inteligente previa a la confirmación del Formulario C | `CP-01` (Detección de cupos vacíos/alcances desaprovechados), `CP-02` (Hallazgos ordenados `alerta → sugerencia → bien`) | `CP-P2-30`, `CP-P2-34` | `formulario-c-estrategia.test.ts`, `formulario-c.estrategia.test.ts` |
| **[US-35](./US-35-aviso-antecedentes-pendientes-impacto-puntaje.md)** | Aviso de antecedentes pendientes con impacto potencial en puntaje | `CP-01` (Cálculo de puntaje antes y después, escuelas/materias nuevas que abre), `CP-02` (No altera el legajo real) | `CP-P2-16`, `CP-P2-31`, `CP-P2-32` | `decisiones.test.ts`, `decisiones.service.test.ts` |
| **[US-36](./US-36-recomendacion-vacantes-convenientes.md)** | Recomendación de vacantes activas que más convienen | `CP-01` (Ordenamiento por nivel, condición y motivo de conveniencia), `CP-02` (Ajuste con parámetro `margen` 0–20) | `CP-P2-33`, `CP-P2-34` | `decisiones.test.ts`, `decisiones.service.test.ts` |
| **[US-37](./US-37-filtrado-escuelas-sugerencias-radio-viaje.md)** | Filtrado de escuelas y sugerencias por radio de viaje en km | `CP-01` (Filtro por `lat`, `lng`, `radioKm` devuelve escuelas dentro del radio), `CP-02` (Radio negativo o zona incompleta devuelve 400) | `CP-P2-28`, `CP-WEB-10` | `formulario-c-estrategia.test.ts`, `zona-util.spec.ts` |

---

## E08 — Mapa Territorial Interactivo y Semáforo LOM

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-38](./US-38-visualizacion-mapa-interactivo-escuelas-departamentos.md)** | Visualización en mapa interactivo de escuelas y departamentos | `CP-01` (Renderizado de escuelas y polígonos IDECOR, incluyendo correcciones de ubicación del lab), `CP-02` (Popup seguro sin inyección HTML) | `CP-P2-09`, `CP-P2-40`, `CP-P2-42`, `CP-WEB-01`, `CP-WEB-14` | `mapa.test.ts`, `oracle-mapa.adapter.test.ts`, `mapa-api.service.spec.ts`, `leaflet.util.spec.ts` |
| **[US-39](./US-39-semaforo-competitividad-lom-mapa.md)** | Semáforo de competitividad LOM en el mapa (`Verde`, `Amarillo`, `Rojo`) | `CP-01` (Verde si promedio ≤ tu puntaje; Amarillo hasta +10 pts; Rojo > +10 pts; Azul sin dato), `CP-02` (Brecha formateada con coma decimal) | `CP-P2-09`, `CP-WEB-07`, `CP-WEB-14` | `mapa.test.ts`, `leaflet.util.spec.ts` |
| **[US-40](./US-40-agrupacion-sedes-madres-anexos-mapa.md)** | Agrupación jerárquica de Sedes Madres con Anexos y Extensiones | `CP-01` (Un punto por sede, vinculación madre por CUE), `CP-02` (Anexo con ID de otra escuela no mezcla LOM ajeno ni permite interés erróneo) | `CP-P2-41`, `CP-WEB-14` | `oracle-mapa.adapter.test.ts`, `leaflet.util.spec.ts` |
| **[US-41](./US-41-filtros-territoriales-curriculares-cascada-mapa.md)** | Filtros en cascada sobre el mapa (Depto, Localidad, Barrio, Semáforo) | `CP-01` (Cascada Departamento → Localidad → Barrio descartando barrios basura `I`, `N`, `SD`), `CP-02` (Filtro por color del semáforo) | `CP-P2-10`, `CP-P2-42`, `CP-P2-43`, `CP-P2-44`, `CP-WEB-14` | `mapa.test.ts`, `oracle-mapa.adapter.test.ts`, `leaflet.util.spec.ts` |
| **[US-42](./US-42-visualizacion-vacantes-publicadas-junta-ficha-mapa.md)** | Visualización de vacantes publicadas por la Junta en el mapa | `CP-01` (Integración con publicaciones de la Junta y acceso desde alertas al mapa), `CP-02` (Escuela sin vacantes mantiene semáforo LOM) | `CP-P2-09`, `CP-P2-48`, `CP-WEB-07` | `mapa.test.ts`, `notificaciones.test.ts` |
| **[US-43](./US-43-registro-baja-muestras-interes-mapa.md)** | Registro y baja de muestras de interés sobre escuelas desde el mapa | `CP-01` (Alta de interés `POST /api/mapa/interes` → 201), `CP-02` (Baja de interés `DELETE` y rechazo de duplicado → 400) | `CP-P2-11`, `CP-P2-12`, `CP-P2-25`, `CP-WEB-01`, `CP-WEB-14` | `mapa.test.ts`, `carrera.test.ts`, `mapa-api.service.spec.ts`, `leaflet.util.spec.ts` |

---

## E09 — Alertas y Cobertura de Vacantes Activas

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-44](./US-44-bandeja-notificaciones-alertas-vacantes.md)** | Bandeja de notificaciones y alertas de nuevas vacantes | `CP-01` (Campana de alertas no leídas, marcar/desmarcar leída y eliminar de bandeja), `CP-02` (Deniega acceso a rol vocal → 403) | `CP-P2-48`, `CP-WEB-19` | `notificaciones.test.ts`, `oracle-notificaciones.adapter.test.ts`, `notificaciones.service.spec.ts` |
| **[US-45](./US-45-filtrado-alertas-vacantes-formulario-c-intereses.md)** | Filtrado de alertas por escuelas del Formulario C, intereses y zona | `CP-01` (Orden prioritario: vigentes → Form C → intereses → resto; filtro `?soloMisEscuelas=true`), `CP-02` (Unificación «Avisame» con intereses del mapa) | `CP-P2-48` | `notificaciones.test.ts` |
| **[US-46](./US-46-consulta-detalle-analitico-vacante.md)** | Consulta del detalle analítico de una solicitud de cobertura | `CP-01` (Exhibición de escuela, asignatura, curso, división, turno, horas y cierre), `CP-02` (Acción directa para ver en mapa o ir al Form C) | `CP-P2-33`, `CP-P2-48` | `notificaciones.test.ts`, `decisiones.test.ts` |
| **[US-47](./US-47-postulacion-voluntaria-cobertura-vacante.md)** | Postulación / manifestación de interés a cobertura de vacante | `CP-01` (Registro de interés/postulación en convocatoria vigente), `CP-02` (Bloqueo de duplicidad o fuera de término) | `CP-P2-11`, `CP-P2-12`, `CP-P2-48` | `mapa.test.ts`, `notificaciones.test.ts` |
| **[US-48](./US-48-emision-comprobante-pdf-postulacion-vacante.md)** | Emisión de comprobante digital de postulación / seguimiento | `CP-01` (Emisión de constancia documental con datos del cargo y sello temporal), `CP-02` (Consulta desde perfil/intereses) | `CP-P2-11`, `CP-P2-48` | `notificaciones.test.ts`, `demo-flow.test.ts` |

---

## E10 — Simulador de Carrera Docente y Recomendador SIPEC

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-49](./US-49-simulador-interactivo-puntaje-lom-sandbox.md)** | Simulador interactivo de puntaje LOM en entorno aislado (Sandbox) | `CP-01` (Adición de cursos, años y título nuevo en sandbox sin alterar legajo real), `CP-02` (Validación de valores inválidos → 400) | `CP-P2-13`, `CP-P2-18`, `CP-P2-19`, `CP-WEB-05`, `CP-WEB-08`, `CP-WEB-12` | `carrera.test.ts`, `simulador.test.ts`, `simulador-calculo.spec.ts` |
| **[US-50](./US-50-proyeccion-escuelas-alcanzables-simulador-carrera.md)** | Proyección de escuelas alcanzables y camino hacia escuela meta | `CP-01` (Listado de escuelas nuevas que abren y camino por años hacia escuela/zona/materia meta), `CP-02` (Cálculo con margen de seguridad) | `CP-P2-18`, `CP-P2-20`, `CP-P2-22`, `CP-P2-23`, `CP-P2-24`, `CP-P2-37`, `CP-P2-38`, `CP-WEB-05`, `CP-WEB-07`, `CP-WEB-09` | `carrera.test.ts`, `carrera.service.test.ts`, `simulador-calculo.spec.ts`, `simulador-plan.spec.ts` |
| **[US-51](./US-51-guardado-comparacion-3-escenarios-simulacion.md)** | Guardado, comparación de escenarios y «Mi plan» exportable | `CP-01` (Comparación de escenarios y guardado de «Mi plan» en sesión), `CP-02` (Exportación de plan en texto y limpieza al cerrar sesión) | `CP-WEB-06`, `CP-WEB-09`, `CP-WEB-13` | `simulador-plan.spec.ts`, `simulador-sesion.spec.ts`, `simulador-calculo.spec.ts` |
| **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** | Recomendador de capacitaciones del catálogo SIPEC (Dto. 257/19) | `CP-01` (Catálogo de 21 cursos SIPEC con filtro por horas y puntos Dto. 257/19), `CP-02` (Deduplicación de ediciones repetidas de un mismo curso) | `CP-P2-14`, `CP-P2-26`, `CP-P2-38`, `CP-WEB-11` | `capacitaciones.test.ts`, `asistente.plan.test.ts`, `simulador-calculo.spec.ts` |
| **[US-53](./US-53-identificacion-rubros-margen-mejora-puntaje.md)** | Identificación de rubros con margen de mejora frente a topes | `CP-01` (Detección de tope de 10 pts en antigüedad y tope 70 en capacitación), `CP-02` (Sugerencia de nuevo título cuando cursos/años alcanzan tope) | `CP-P2-36`, `CP-P2-39`, `CP-WEB-12` | `decreto-257.test.ts`, `asistente.test.ts`, `simulador-calculo.spec.ts` |

---

## E11 — Asistente Virtual Docente con IA (Chatbot + LLM)

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-54](./US-54-interfaz-conversacion-sugerencias-asistente-virtual.md)** | Interfaz de conversación y sugerencias rápidas del Asistente | `CP-01` (Avisos proactivos al abrir, chips de sugerencias rápidas y enlaces de navegación), `CP-02` (Validación de mensaje vacío) | `CP-P2-26`, `CP-P2-49`, `CP-WEB-19` | `asistente.test.ts`, `asistente.service.spec.ts` |
| **[US-55](./US-55-respuestas-contextualizadas-datos-reales-asistente.md)** | Respuestas contextualizadas con los datos reales del docente | `CP-01` (Respuestas con rechazos del legajo, puntaje, escuelas cercanas, vacantes y plan de cursos), `CP-02` (Aislamiento al CUIL de la sesión) | `CP-P2-26`, `CP-P2-38`, `CP-P2-39`, `CP-P2-49` | `asistente.test.ts`, `asistente.datos.test.ts`, `asistente.consultas.test.ts`, `asistente.plan.test.ts` |
| **[US-56](./US-56-interpretacion-lenguaje-natural-llm-groq.md)** | Interpretación de lenguaje natural mediante LLM (Groq) | `CP-01` (Clasificación de intención y consulta sobre datos del dominio con Groq), `CP-02` (Consultas fuera de dominio redirigidas cortésmente) | `CP-P2-49` | `asistente.llm.test.ts`, `asistente.agente.test.ts`, `asistente.nlu.test.ts` |
| **[US-57](./US-57-contingencia-deterministica-local-asistente.md)** | Contingencia determinística local ante falta de clave o caída de LLM | `CP-01` (Operación completa sin `GROQ_API_KEY` usando motor NLU local), `CP-02` (Fallback transparente ante error/timeout de red externa) | `CP-P2-49` | `asistente.llm.test.ts`, `asistente.nlu.test.ts`, `asistente.test.ts` |
| **[US-58](./US-58-resguardo-datos-personales-pii-consultas-llm.md)** | Resguardo de datos personales (PII) en consultas al LLM externo | `CP-01` (Exclusión de tokens, contraseñas y binarios en el prompt enviado al LLM; control `LLM_ENVIAR_PERFIL`), `CP-02` (Control de longitud de entrada) | `CP-P2-49` | `asistente.llm.test.ts`, `asistente.agente.test.ts` |

---

## E12 — Gestión y Fiscalización para Vocales de Junta

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-59](./US-59-bandeja-establecimientos-vocal-aislamiento-jurisdiccion.md)** | Bandeja de establecimientos asignados al Vocal con aislamiento | `CP-01` (Vocal 1 ve sus escuelas asignadas), `CP-02` (Vocal 2 ve `ESC-003`), `CP-03` (Intento de acceso cruzado devuelve 403/404) | `CP-P2-03`, `CP-P2-05` | `junta.test.ts` |
| **[US-60](./US-60-listado-busqueda-filtrado-aspirantes-por-escuela.md)** | Listado, búsqueda y filtrado de aspirantes por escuela y estado | `CP-01` (Filtro por estado `Pendiente` / `Sin Pendiente`), `CP-02` (Inclusión de aspirantes que confirmaron Formulario C en esa escuela) | `CP-P2-04` | `junta.test.ts` |
| **[US-61](./US-61-inspeccion-integral-lupa-legajo-inscripcion-vocal.md)** | Inspección integral («Lupa») del legajo e inscripción por el Vocal | `CP-01` (Visualización de antecedentes, PDFs y marca «Sin título habilitante»), `CP-02` (Bloqueo si el `escuelaId` no pertenece al vocal) | `CP-P1-14`, `CP-P2-04`, `CP-WEB-18` | `junta.test.ts`, `junta-antecedentes.spec.ts` |
| **[US-62](./US-62-aprobacion-aceptacion-antecedentes-vocal.md)** | Aprobación (Aceptación) de antecedentes del legajo por el Vocal | `CP-01` (`POST .../aceptar` pasa antecedente a `VALIDADO` y aspirante a `SIN_PENDIENTE`), `CP-02` (Impacto inmediato en legajo y puntaje del docente) | `CP-P2-06`, `CP-WEB-18` | `junta.test.ts`, `demo-flow.test.ts`, `junta-antecedentes.spec.ts` |
| **[US-63](./US-63-rechazo-fundado-antecedentes-motivo-obligatorio-vocal.md)** | Rechazo fundado de antecedentes con motivo obligatorio (≥ 10 car.) | `CP-01` (`POST .../rechazar` con motivo ≥ 10 caracteres pasa a `RECHAZADO`), `CP-02` (Motivo vacío o < 10 caracteres es rechazado en UI y API) | `CP-P2-07`, `CP-P2-08`, `CP-WEB-18` | `junta.test.ts`, `demo-flow.test.ts`, `rechazo-dialog.component.spec.ts` |
| **[US-64](./US-64-sincronizacion-escritura-espejo-tablas-junta.md)** | Sincronización y escritura espejo en las tablas de la Junta | `CP-01` (Mapeo y escritura en lista cerrada de 9 tablas en modo `on` y `ROLLBACK` en modo `ensayo`), `CP-02` (Protección de solo lectura fuera de lista blanca) | `CP-P2-50` | `oracle-junta.test.ts`, `oracle-solo-lectura.test.ts`, `oracle-resguardo.test.ts` |

---

## E13 — Gestión de Reclamos, Historial LOM y Analítica

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-65](./US-65-presentacion-digital-reclamos-revision-lom.md)** | Presentación digital de reclamos de revisión de LOM o antecedentes | `CP-01` (Apertura de reclamo en ventana habilitada con descargo), `CP-02` (Bloqueo fuera de ventana de exhibición) | `CP-P2-08`, `CP-P2-16` | `demo-flow.test.ts`, `puntaje.test.ts` |
| **[US-66](./US-66-bandeja-resolucion-formal-reclamos-vocal.md)** | Bandeja de resolución formal de reclamos para Vocales | `CP-01` (Resolución fundamentada y actualización de dictamen), `CP-02` (Validación de longitud mínima de fundamentación), `CP-03` (Inmutabilidad post-cierre) | `CP-P2-06`, `CP-P2-07`, `CP-WEB-18` | `junta.test.ts`, `rechazo-dialog.component.spec.ts` |
| **[US-67](./US-67-consulta-historial-inscripciones-lom-ciclos-anteriores.md)** | Consulta de historial de inscripciones y LOM de ciclos anteriores | `CP-01` (Consulta de inscripciones y LOM históricas por CUIL), `CP-02` (Estado vacío para ingresantes sin ciclos previos) | `CP-P2-01`, `CP-P2-46` | `oracle-lom.adapter.test.ts`, `perfil.test.ts` |
| **[US-68](./US-68-visualizacion-metricas-percentiles-competitividad-regional.md)** | Visualización de métricas anonimizadas de competitividad regional | `CP-01` (Comparación contra promedios departamentales/por escuela en `T_DETALLES_INSC_JD`), `CP-02` (Ausencia total de PII de otros docentes) | `CP-P2-09`, `CP-P2-23` | `mapa.test.ts`, `carrera.test.ts` |
| **[US-69](./US-69-exportacion-reportes-operativos-gestion-vocales.md)** | Exportación de reportes operativos de gestión para Vocales | `CP-01` (Generación de resumen de estado de revisión por escuela), `CP-02` (Rechazo 403 para rol `aspirante`) | `CP-P2-03`, `CP-P2-04`, `CP-WEB-09` | `junta.test.ts`, `simulador-plan.spec.ts` |

---

## E14 — Gobernanza, Seguridad y Auditoría Inmutable

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-70](./US-70-registro-inmutable-eventos-acceso-sesion.md)** | Registro inmutable de eventos de acceso y sesión (`auditoria_acceso`) | `CP-01` (Registra ingreso y salida con timestamp y CUIL), `CP-02` (Registra intento rechazado sin almacenar contraseña), `CP-03` (Carácter `APPEND-ONLY`) | `CP-P2-51` | `auditoria.test.ts` |
| **[US-71](./US-71-bitacora-inmutable-operaciones-legajo-formulario-c.md)** | Bitácora inmutable de operaciones sobre legajo y Formulario C | `CP-01` (Registra alta/baja de antecedente sin guardar binario PDF y guardado de Form C), `CP-02` (Inmutabilidad `APPEND-ONLY` en `auditoria_cambio`) | `CP-P2-51` | `auditoria.test.ts` |
| **[US-72](./US-72-registro-auditable-dictamenes-validacion-rechazo-vocales.md)** | Registro auditable de dictámenes de validación y rechazo de Vocales | `CP-01` (Asiento auditable al aceptar antecedente con CUIL de vocal y aspirante), `CP-02` (Asiento auditable al rechazar conservando el motivo) | `CP-P2-51` | `auditoria.test.ts` |
| **[US-73](./US-73-consulta-filtrado-historial-auditoria-accesos-cambios.md)** | Consulta y filtrado del historial de auditoría de accesos y cambios | `CP-01` (Consulta cronológica de eventos registrados), `CP-02` (Protección de solo lectura y control de acceso por rol) | `CP-P2-51` | `auditoria.test.ts` |
| **[US-74](./US-74-control-acceso-rbac-proteccion-secretos.md)** | Control de acceso basado en roles (RBAC) y protección de secretos | `CP-01` (Bloqueo 401/403 ante acceso cruzado de rol o manipulación de CUIL), `CP-02` (Validación de variables de entorno y `JWT_SECRET` en producción) | `CP-P2-21`, `CP-P2-34`, `CP-P2-46`, `CP-P2-48` | `env.test.ts`, `junta.test.ts`, `carrera.test.ts`, `decisiones.test.ts`, `perfil.test.ts`, `notificaciones.test.ts` |

---

## Sin Épica (S/E) — Transversales, Operación e Interoperabilidad

| ID HU | Historia de Usuario | Casos de Prueba de Aceptación (en la HU) | IDs Casos Automatizados | Suites de Test Ejecutables |
| :---: | :--- | :--- | :---: | :--- |
| **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** | Conmutación de modo de integración (`prod` / `foto` / `mock`) y rótulos | `CP-01` (Arranque en `prod` con Oracle Lab o `foto` sin VPN), `CP-02` (Arranque en `mock` con consistencia de fixtures y rótulos de ejemplo) | `CP-P2-15`, `CP-P2-52` | `app.test.ts`, `foto-lab.test.ts`, `fixture-consistency.test.ts` |
| **[US-76](./US-76-parametrizacion-ventana-convocatoria-ciclo-lectivo.md)** | Parametrización de ventana de convocatoria anual (`INSCRIPCION_ABIERTA`) | `CP-01` (Convocatoria abierta permite inscripción), `CP-02` (`INSCRIPCION_ABIERTA=false` pasa a solo consulta) | `CP-P1-12` | `mvp-chain.test.ts`, `env.test.ts` |
| **[US-77](./US-77-monitoreo-salud-operativa-conectividad-health.md)** | Monitoreo de salud operativa y diagnóstico (`/api/health`) | `CP-01` (`GET /api/health` responde 200 con estado de módulos e `integrationMode`), `CP-02` (No expone credenciales ni secretos) | `CP-P2-15`, `CP-WEB-19` | `app.test.ts`, `demo-flow.test.ts`, `health-api.service.spec.ts` |
| **[US-78](./US-78-resiliencia-degradacion-elegante-servicios-externos.md)** | Resiliencia y degradación elegante ante fallos de servicios externos | `CP-01` (Tolerancia a errores de escritura en cola de persistencia sin tumbar el proceso), `CP-02` (Fallback ante error de red externa) | `CP-P2-49`, `CP-P2-52` | `postgres.persistencia.test.ts`, `store-persistencia.test.ts`, `asistente.llm.test.ts` |
| **[US-79](./US-79-diseno-adaptable-responsive-accesibilidad-web.md)** | Diseño adaptable (*Responsive*) y criterios de accesibilidad web | `CP-01` (Disposición responsiva en móvil y escritorio), `CP-02` (Etiquetas textuales junto a colores de semáforo y estados) | `CP-WEB-14` | `leaflet.util.spec.ts` |
| **[US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md)** | Despliegue en Docker e inicialización automática de esquemas y semillas | `CP-01` (Creación idempotente de esquema PostgreSQL y carga de semilla), `CP-02` (Conservación de datos existentes al reiniciar) | `CP-P2-52` | `postgres.persistencia.test.ts`, `store-persistencia.test.ts`, `env.test.ts` |

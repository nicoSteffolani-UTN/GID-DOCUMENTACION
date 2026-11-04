# Matriz de Trazabilidad: Tablero Jira (`PD`) ↔ Sprints (`0` a `12`) ↔ 80 Historias de Usuario (`US-01` a `US-80`)

**Proyecto Final — Gestión Integral Docente (GID) — Grupo 24 (UTN-FRC)**  
**Fuente Jira:** [`Jira.csv`](../Jira.csv) — Proyecto `PD` (*Portal Docente - Tesis - 2026*)  
**Catálogo de Historias de Usuario:** [`README.md`](./README.md) (`US-01` a `US-80`)  
**Matriz de Casos de Prueba:** [`MATRIZ_CASOS_DE_PRUEBA.md`](./MATRIZ_CASOS_DE_PRUEBA.md)

---

## 1. Resumen del Cruce entre Jira y el Nuevo Catálogo de 80 HUs

Del análisis del archivo [`Jira.csv`](../Jira.csv) (65 incidencias totales: **9 Épicas** `PD-22` a `PD-30` y **56 Historias/Tareas** `PD-31` a `PD-79` distribuidas entre `PD Sprint 0` y `PD Sprint 12`), surge cómo evolucionó el desarrollo del equipo y cómo se conecta con las **80 Historias de Usuario (`US-01` a `US-80`)**:

1. **De historias macro (MVP / Instancia 2) a las 80 HUs atómicas:**
   - En Jira se utilizaban inicialmente los códigos de *Instancia 2 / MVP* (`US-01`, `US-03`, `US-04`, `US-05`, `US-06`, `US-07`, `US-10 lite`, `US-11`, `US-12`, `US-13`, `US-14`, `PD-21`), donde una sola tarjeta de Jira agrupaba todo un módulo (por ejemplo, `PD-39` y `PD-43` para todo el Formulario C, o `PD-47` y `PD-66` para toda la Bandeja de Vocales de Junta).
   - Asimismo, los desarrollos de la recta final de **octubre de 2026 (Sprints 10, 11 y 12)** quedaron en el CSV en estado `validar` / `En curso` / `Por hacer` (`PD-61` CiDi OAuth, `PD-63` Simulador/Desglose LOM, `PD-69` Postulaciones/Ayudas, `PD-70` Panel de Docente, `PD-73` Chatbot IA, `PD-74`/`PD-78` Notificador y `PD-79` Persistencia PostgreSQL) y se completaron en el repositorio mediante los PRs `#62` a `#76` (Motor Dto. 257/19, Ayudas para decidir, Espejo SGE/Junta ADR-029, Antigüedad PeopleNet, CiDi ADR-031 y Auditoría Inmutable ADR-032).
2. **Trazabilidad bidireccional completa:**
   - **Sección 2:** Equivalencia entre las **9 Épicas de Jira (`PD-22` a `PD-30`)** y las **14 Épicas nuevas (`E01` a `E14` + `S/E`)**.
   - **Sección 3:** Cronología **Sprint por Sprint (`PD Sprint 0` a `PD Sprint 12`)** indicando qué HUs se construyeron en cada Sprint.
   - **Sección 4:** Tabla Maestra de las **80 HUs (`US-01` a `US-80`)** con su ticket `PD-XX`, Sprint, Story Points y archivo `.md`.
   - **Sección 5:** Mapa inverso rápido **Ticket Jira (`PD-31` a `PD-79`) → Nuevas HUs (`US-01` a `US-80`)**.

---

## 2. Equivalencia de Épicas: Jira (`PD-22`..`PD-30`) ↔ Nuevo Catálogo (`E01`..`E14` + `S/E`)

| Épica en Jira (`Jira.csv`) | Resumen en Jira | Épica(s) del Nuevo Catálogo (`E01`–`E14` + `S/E`) | HUs Asociadas (`US-01`–`US-80`) | Cantidad de HUs | Story Points (SP) |
| :--- | :--- | :--- | :--- | :---: | :---: |
| **`PD-22`** | Autenticación (Mock actual → CiDi Real) | **E01** — Identidad Digital, Autenticación y Sesión (CiDi) | `US-01` a `US-05` | 5 | 22 |
| **`PD-23`** | Perfil | **E02** — Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet) | `US-06` a `US-10` | 5 | 18 |
| **`PD-24`** | Legajo | **E03** — Gestión del Legajo Digital y Antecedentes | `US-11` a `US-16` | 6 | 24 |
| **`PD-27`** | Puntaje | **E04** — Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19) | `US-17` a `US-21` | 5 | 26 |
| **`PD-26`** | Validador | **E05** — Validador de Alcance de Títulos y Competencias | `US-22` a `US-26` | 5 | 19 |
| **`PD-25`** | Formulario C | **E06** — Convocatoria Anual e Inscripción en Formulario C<br>**E07** — Ayudas a la Decisión y Recomendación de Postulación | `US-27` a `US-32` (E06)<br>`US-33` a `US-37` (E07) | 11 | 52 |
| **`PD-29`** | Mapa Territorial | **E08** — Mapa Territorial Interactivo y Semáforo LOM | `US-38` a `US-43` | 6 | 34 |
| **`PD-30`** | Autogestión y Simulador (Spikes) | **E09** — Alertas y Cobertura de Vacantes Activas<br>**E10** — Simulador de Carrera Docente y Recomendador SIPEC<br>**E11** — Asistente Virtual Docente con IA (Chatbot + LLM) | `US-44` a `US-48` (E09)<br>`US-49` a `US-53` (E10)<br>`US-54` a `US-58` (E11) | 15 | 57 |
| **`PD-28`** | Junta Vocal | **E12** — Gestión y Fiscalización para Vocales de Junta<br>**E13** — Gestión de Reclamos, Historial LOM y Analítica<br>**E14** — Gobernanza, Seguridad y Auditoría Inmutable | `US-59` a `US-64` (E12)<br>`US-65` a `US-69` (E13)<br>`US-70` a `US-74` (E14) | 16 | 70 |
| *(Tareas técnicas S/E)* | Tareas de Infraestructura, BD, VPN y CI/CD | **S/E** — Historias Transversales, Operación e Interoperabilidad | `US-75` a `US-80` | 6 | 25 |
| **TOTAL** | **9 Épicas en Jira** | **14 Épicas de Producto + 1 Bloque Transversal (`S/E`)** | **`US-01` a `US-80`** | **80** | **347 SP** |

---

## 3. Trazabilidad Cronológica Sprint por Sprint (`PD Sprint 0` a `PD Sprint 12`)

### `PD Sprint 0` — Cimientos, Arquitectura y Acuerdos de Trabajo
| Clave Jira | Tipo | Resumen en Jira | Estado Jira | Responsable | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-31`** | Tarea | Definición de Working Agreement y roles | `cerrado` | Fabrizio Valverde Segura | Base organizativa del equipo G24 |
| **`PD-32`** | Tarea | Creación de política de ramas y PR (SCM) | `cerrado` | Alexis Demaria | Base para [US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md) |
| **`PD-33`** | Tarea | Diagramas de Arquitectura (C4) | `cerrado` | Matias Agustin Formoso | Diseño base para [US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md) y [US-78](./US-78-resiliencia-degradacion-elegante-servicios-externos.md) |
| **`PD-34`** | Tarea | Creación de repositorio base en GitHub | `cerrado` | Nicolas Steffolani | Base para [US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md) |

---

### `PD Sprint 1` — Monorepo, CI/CD, Usuarios Semilla y Arnés de Ingreso Simulado Temporal
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-35`** | Historia | `PD-30` / `PD-22` | US-01: Autenticarse (Fase 1: Mock local — el logueo real por CiDi queda en Backlog) | `cerrado` | **[US-05](./US-05-arnes-ingreso-simulado-temporal.md)** (Arnés de ingreso simulado temporal), **[US-03](./US-03-bifurcacion-entorno-rol.md)** (Bifurcación Aspirante/Vocal), **[US-04](./US-04-expiracion-inactividad-cierre-sesion.md)** (Cierre seguro de sesión) |
| **`PD-36`** | Tarea | S/E | Configurar monorepo (Angular + Node) y pipeline CI | `cerrado` | **[US-77](./US-77-monitoreo-salud-operativa-conectividad-health.md)** (`/api/health`), **[US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md)** (Entorno reproducible y CI) |
| **`PD-37`** | Tarea | S/E | Creación de fixtures de usuarios demo | `cerrado` | **[US-05](./US-05-arnes-ingreso-simulado-temporal.md)** (Perfiles demo semilla), **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** (Modo `mock`) |

---

### `PD Sprint 2` — Backend API Core: Legajo, Formulario C (Regla 3×5), Validador y Modo SQL Offline
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-38`** | Historia | `PD-24` (Legajo) | US-03: Actualizar legajo y antecedentes (Backend API) | `cerrado` | **[US-11](./US-11-carga-manual-antecedentes-pdf.md)** (Carga manual PDF), **[US-12](./US-12-sincronizacion-automatica-antecedentes-sge.md)** (Sync provincial SGE), **[US-13](./US-13-consulta-listado-estados-legajo.md)** (Listado y estados), **[US-15](./US-15-baja-antecedentes-manuales-pendientes.md)** (Baja de pendientes) |
| **`PD-39`** | Historia | `PD-25` (Form C) | US-04: Completar Formulario C (Reglas de negocio 3 escuelas × 5 ítems) | `cerrado` | **[US-28](./US-28-confeccion-formulario-c-tope-3x5.md)** (Tope 3×5), **[US-29](./US-29-guardado-borrador-edicion-formulario-c.md)** (Borrador y edición), **[US-30](./US-30-confirmacion-definitiva-formulario-c.md)** (Confirmación definitiva), **[US-32](./US-32-bloqueo-modificacion-fuera-ventana-convocatoria.md)** (Bloqueo fuera de ventana) |
| **`PD-40`** | Historia | `PD-26` (Validador) | US-07: Validar título (Habilitación por asignatura API) | `cerrado` | **[US-22](./US-22-consulta-catalogo-titulos-busqueda-competencias.md)** (Catálogo de títulos y competencias), **[US-23](./US-23-validacion-tiempo-real-titulo-asignatura.md)** (Compatibilidad Título–Asignatura), **[US-24](./US-24-busqueda-inversa-titulos-por-materia.md)** (Búsqueda inversa por materia) |
| **`PD-41`** | Tarea | S/E | Implementación modo offline (`INTEGRATION_MODE=sql`) | `cerrado` | **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** (Conmutación de modo de integración) |

---

### `PD Sprint 3` — Frontend UI Core: Pantallas de Legajo, Formulario C y Validador
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-42`** | Historia | `PD-24` (Legajo) | US-03: UI Pantallas de carga de legajo y adjuntos | `cerrado` | **[US-11](./US-11-carga-manual-antecedentes-pdf.md)** (UI carga PDF), **[US-13](./US-13-consulta-listado-estados-legajo.md)** (UI estados legajo), **[US-14](./US-14-previsualizacion-descarga-certificado-pdf.md)** (Previsualización PDF), **[US-16](./US-16-consulta-observaciones-motivos-rechazo.md)** (Observaciones de rechazo), **[US-79](./US-79-diseno-adaptable-responsive-accesibilidad-web.md)** (UI responsive) |
| **`PD-43`** | Historia | `PD-25` (Form C) | US-04: UI Pantalla Formulario C con catálogo | `cerrado` | **[US-27](./US-27-consulta-busqueda-padron-escuelas-oferta-curricular.md)** (Padrón de escuelas y oferta), **[US-28](./US-28-confeccion-formulario-c-tope-3x5.md)** (UI 3×5), **[US-29](./US-29-guardado-borrador-edicion-formulario-c.md)**, **[US-30](./US-30-confirmacion-definitiva-formulario-c.md)**, **[US-31](./US-31-emision-descarga-comprobante-pdf-formulario-c.md)** (Comprobante PDF Form C) |
| **`PD-44`** | Historia | `PD-26` (Validador) | US-07: UI Integración validador en interfaz | `cerrado` | **[US-22](./US-22-consulta-catalogo-titulos-busqueda-competencias.md)** (UI Validador), **[US-23](./US-23-validacion-tiempo-real-titulo-asignatura.md)** (Validación en tiempo real), **[US-25](./US-25-advertencia-preventiva-materia-sin-alcance.md)** (Advertencia preventiva sin alcance) |

---

### `PD Sprint 4` — Puntaje de Inscripción (Fase Mock) y Spike Mapa Territorial Leaflet
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-46`** | Historia | `PD-27` (Puntaje) | US-05: Consultar puntaje de inscripción (Mock) | `cerrado` | **[US-17](./US-17-consulta-puntaje-desglose-decreto-257.md)** (Consulta de puntaje), **[US-19](./US-19-consulta-puntaje-por-escuela-materia.md)** (Puntaje por escuela y materia), **[US-20](./US-20-estimacion-automatica-puntaje-sin-valoracion-previa.md)** (Caso sin valoración previa) |
| **`PD-48`** | Historia | `PD-29` (Mapa) | PD-21: Mapa de vacantes territorial (Leaflet mock) | `cerrado` | **[US-38](./US-38-visualizacion-mapa-interactivo-escuelas-departamentos.md)** (Mapa interactivo de escuelas) |
| **`PD-49`** | Historia | `PD-29` (Mapa) | US-10 lite: Registrar interés desde mapa | `cerrado` | **[US-43](./US-43-registro-baja-muestras-interes-mapa.md)** (Registro y baja de muestras de interés desde el mapa) |

---

### `PD Sprint 5` — Bandeja de Vocales de Junta (Fase 1), Simulador/Capacitaciones (Fase 1) y VPN Ministerial
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-47`** | Historia | `PD-28` (Junta) | US-06: Revisar aspirantes por escuela (UI Cruda y Bugs) | `cerrado` | **[US-59](./US-59-bandeja-establecimientos-vocal-aislamiento-jurisdiccion.md)** (Bandeja vocal y aislamiento), **[US-60](./US-60-listado-busqueda-filtrado-aspirantes-por-escuela.md)** (Listado aspirantes), **[US-61](./US-61-inspeccion-integral-lupa-legajo-inscripcion-vocal.md)** (Lupa legajo), **[US-62](./US-62-aprobacion-aceptacion-antecedentes-vocal.md)** (Aceptación), **[US-63](./US-63-rechazo-fundado-antecedentes-motivo-obligatorio-vocal.md)** (Rechazo con motivo ≥ 10 car.) |
| **`PD-50`** | Historia | `PD-30` (Simulador) | US-11 / US-12: Simulador de ascenso y capacitaciones | `cerrado` | **[US-49](./US-49-simulador-interactivo-puntaje-lom-sandbox.md)** (Simulador interactivo sandbox), **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** (Recomendador capacitaciones SIPEC) |
| **`PD-51`** | Tarea | S/E | Configuración VPN OpenVPN del Ministerio | `cerrado` | **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** y **[US-78](./US-78-resiliencia-degradacion-elegante-servicios-externos.md)** (Conectividad VPN y resiliencia externa) |
| **`PD-52`** | Tarea | S/E | Alineación de fixtures demo a nombres del dump | `cerrado` | **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** y **[US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md)** (Datos semilla alineados al dump ministerial) |

---

### `PD Sprint 6` — Integración Real con Oracle Lab (Fase 1: Credenciales, Vistas y Validador)
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-45`** | Tarea | S/E | Gestionar credenciales y conexión a Oracle Lab (Rollover Sprint 3 a 6) | `cerrado` | **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** (Modo `prod` Oracle Lab), **[US-77](./US-77-monitoreo-salud-operativa-conectividad-health.md)** (Diagnóstico de conectividad BD) |
| **`PD-53`** | Historia | `PD-26` (Validador) | US-07: Conexión Validador alcance a Oracle (`VT_MAT_GRADO_X_TITULO`) | `cerrado` | **[US-22](./US-22-consulta-catalogo-titulos-busqueda-competencias.md)** (Consulta real en `VT_MAT_GRADO_X_TITULO`), **[US-24](./US-24-busqueda-inversa-titulos-por-materia.md)** (Búsqueda inversa en Oracle) |
| **`PD-54`** | Tarea | S/E | Refactor por problema `ORA-00942` (Migración de tablas a vistas en BD) | `cerrado` | **[US-78](./US-78-resiliencia-degradacion-elegante-servicios-externos.md)** (Resiliencia ante esquemas/vistas externas) |

---

### `PD Sprint 7` — Integración Real con Oracle Lab (Fase 2: Padrón Form C, Puntaje LOM y Mapa)
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-55`** | Historia | `PD-29` (Mapa) | PD-21: Integración Mapa vacantes con Oracle (coordenadas y localidad) | `cerrado` | **[US-38](./US-38-visualizacion-mapa-interactivo-escuelas-departamentos.md)** (Escuelas georreferenciadas desde Oracle), **[US-42](./US-42-visualizacion-vacantes-publicadas-junta-ficha-mapa.md)** (Vacantes publicadas por Junta) |
| **`PD-56`** | Historia | `PD-27` (Puntaje) | US-05: Integración Puntaje LOM parcial (`PJE_DEFINITIVO` desde Oracle) | `cerrado` | **[US-17](./US-17-consulta-puntaje-desglose-decreto-257.md)** (`PJE_DEFINITIVO`), **[US-19](./US-19-consulta-puntaje-por-escuela-materia.md)**, **[US-21](./US-21-distincion-lom-provisoria-definitiva.md)** (LOM provisoria vs. definitiva) |
| **`PD-57`** | Historia | `PD-25` (Form C) | US-04: Conexión Padrón y catálogo Form C a Oracle | `cerrado` | **[US-27](./US-27-consulta-busqueda-padron-escuelas-oferta-curricular.md)** (Padrón oficial de escuelas con CUE y oferta curricular desde Oracle) |

---

### `PD Sprint 8` — Semáforo LOM Real, Catálogo SIPEC (Dto. 257/19) y Rediseño UI de Junta y Mapa
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-58`** | Historia | `PD-29` (Mapa) | PD-21 / US-10: Mejoras estéticas de UI/UX en Mapa (exclusión directivos, revisión escuelas) | `cerrado` | **[US-38](./US-38-visualizacion-mapa-interactivo-escuelas-departamentos.md)** (Límites IDECOR), **[US-40](./US-40-agrupacion-sedes-madres-anexos-mapa.md)** (Agrupación Sedes Madres y Anexos), **[US-41](./US-41-filtros-territoriales-curriculares-cascada-mapa.md)** (Filtros en cascada), **[US-42](./US-42-visualizacion-vacantes-publicadas-junta-ficha-mapa.md)** (Exclusión directivos) |
| **`PD-60`** | Historia | `PD-30` (Simulador) | US-12: Conexión con listado de Cursos de Capacitación | `cerrado` | **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** (Catálogo oficial SIPEC conectado) |
| **`PD-64`** | Tarea | `PD-30` / `PD-27` | Obtener información de cuánto puntaje está dado por cada curso (`horas × 0,017`) | `cerrado` | **[US-18](./US-18-ponderacion-automatica-capacitaciones-titulos.md)** y **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** (Ponderación Dto. 257/19 Art. 10: cursos ≥ 20 hs = `horas × 0,017`) |
| **`PD-66`** | Historia | `PD-28` (Junta) | Mejora de UI/UX en bandeja de vocales (40 escuelas, >200 aspirantes, panel lateral) | `cerrado` | **[US-59](./US-59-bandeja-establecimientos-vocal-aislamiento-jurisdiccion.md)**, **[US-60](./US-60-listado-busqueda-filtrado-aspirantes-por-escuela.md)** y **[US-61](./US-61-inspeccion-integral-lupa-legajo-inscripcion-vocal.md)** (Bandeja a escala, filtros y lupa lateral) |
| **`PD-67`** | Tarea | `PD-27` / `PD-29` | Spike para conocer el funcionamiento del cálculo de LOM actual | `cerrado` | **[US-17](./US-17-consulta-puntaje-desglose-decreto-257.md)**, **[US-18](./US-18-ponderacion-automatica-capacitaciones-titulos.md)** y **[US-39](./US-39-semaforo-competitividad-lom-mapa.md)** (Semáforo LOM real sobre `T_DETALLES_INSC_JD` — top 5 / mín. 10 inscriptos) |

---

### `PD Sprint 9` y `PD Sprint 10` — Reglas de Alcance Supletorio, Panel Docente, Filtros, Chatbot y Alertas
| Clave Jira | Tipo | Épica Jira | Resumen en Jira | Estado Jira (al 20/10) | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-65`** | Tarea | `PD-26` / `PD-25` | Definir comportamiento de alcance título e inscripción como supletorio y manejo de escuelas base | `cerrado` | **[US-09](./US-09-visualizacion-cargos-vigentes-trayectoria.md)** (Cargos e historial de escuelas), **[US-23](./US-23-validacion-tiempo-real-titulo-asignatura.md)**, **[US-25](./US-25-advertencia-preventiva-materia-sin-alcance.md)**, **[US-26](./US-26-marcado-automatico-sin-titulo-habilitante-vocal.md)** (Marcado «Sin título habilitante» para el vocal) |
| **`PD-70`** | Historia | `PD-30` / `PD-23` | US-14: Panel de docente (bandeja unificada de puntaje, títulos, notificaciones e inscripciones) | `validar` *(Implementado)* | **[US-06](./US-06-panel-inicio-estado-ciclo.md)** (Panel de inicio del ciclo), **[US-07](./US-07-consulta-datos-personales-perfil.md)** (Mi Perfil), **[US-08](./US-08-consulta-calculo-antiguedad-peoplenet.md)** (Antigüedad PeopleNet), **[US-09](./US-09-visualizacion-cargos-vigentes-trayectoria.md)** (Cargos vigentes), **[US-10](./US-10-configuracion-ubicacion-radio-traslado.md)** (Ubicación y radio de traslado) |
| **`PD-71`** | Historia | `PD-25` (Form C) | US-MEJORA: Agregar filtros de búsqueda para las materias listadas dentro del plan de estudio (año/especialidad) | `validar` *(Implementado)* | **[US-27](./US-27-consulta-busqueda-padron-escuelas-oferta-curricular.md)** (Filtros por año académico y especialidad en oferta curricular) |
| **`PD-72`** | Tarea | `PD-26` (Validador) | Verificar si la vista de materias por título se puede relacionar con el nivel educativo / carácter | `validar` *(Implementado)* | **[US-18](./US-18-ponderacion-automatica-capacitaciones-titulos.md)**, **[US-22](./US-22-consulta-catalogo-titulos-busqueda-competencias.md)** y **[US-23](./US-23-validacion-tiempo-real-titulo-asignatura.md)** (Cruce de carácter Docente / Habilitante / Supletorio) |
| **`PD-73`** | Historia | `PD-30` (Asistente) | US: Desarrollar un chat-bot | `validar` *(Implementado)* | **[US-54](./US-54-interfaz-conversacion-sugerencias-asistente-virtual.md)** (UI y sugerencias), **[US-55](./US-55-respuestas-contextualizadas-datos-reales-asistente.md)** (Contexto real del docente), **[US-57](./US-57-contingencia-deterministica-local-asistente.md)** (Contingencia determinística), **[US-58](./US-58-resguardo-datos-personales-pii-consultas-llm.md)** (Resguardo PII) |
| **`PD-74`** | Historia | `PD-30` (Alertas) | US: Notificador de postulación (configurable por escuela, materia, localidad y barrio) | `validar` *(Implementado)* | **[US-44](./US-44-bandeja-notificaciones-alertas-vacantes.md)** (Bandeja de alertas de vacantes), **[US-45](./US-45-filtrado-alertas-vacantes-formulario-c-intereses.md)** (Filtrado por Form C, intereses y departamento) |
| **`PD-75`** | Tarea | `PD-30` (Simulador) | Filtrar listado de cursos en base a la localidad y si aportan a las materias que está en condiciones de dictar | `Por hacer` *(Resuelto en UI)* | **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** (Filtros del recomendador de capacitaciones SIPEC) |
| **`PD-77`** | Historia | `PD-30` (Simulador) | US-MEJORA: Filtros para cursos | `Por hacer` *(Resuelto en UI)* | **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** (Buscador y filtrado del catálogo SIPEC) |

---

### `PD Sprint 11` y `PD Sprint 12` — Cierre de Producto: Persistencia PostgreSQL, Motor Dto. 257/19, Ayudas a la Decisión, CiDi OAuth2, Espejo Junta y Auditoría Inmutable
| Clave Jira | Tipo | Épica Jira | Resumen en Jira / Entregable de Cierre (Octubre 2026) | Estado Jira / Repo | Conexión con Nuevas HUs (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`PD-63`** | Historia | `PD-27` / `PD-30` | US-05: SIMULADOR DE AVANCE DE CARRERA (Desglose completo de LOM Dto. 257/19) | `En curso` *(Cerrado en PR #65/#71)* | **[US-17](./US-17-consulta-puntaje-desglose-decreto-257.md)**, **[US-18](./US-18-ponderacion-automatica-capacitaciones-titulos.md)**, **[US-20](./US-20-estimacion-automatica-puntaje-sin-valoracion-previa.md)** (Estimación sin valoración previa), **[US-49](./US-49-simulador-interactivo-puntaje-lom-sandbox.md)**, **[US-50](./US-50-proyeccion-escuelas-alcanzables-simulador-carrera.md)** (Escuelas alcanzables), **[US-51](./US-51-guardado-comparacion-3-escenarios-simulacion.md)** (3 escenarios), **[US-53](./US-53-identificacion-rubros-margen-mejora-puntaje.md)** (Rubros con margen de mejora) |
| **`PD-69`** | Historia | `PD-25` (Form C) | US-13: Conexión de postulación de cargos con las inscripciones del docente y Ayudas para decidir | `validar` *(Cerrado en PR #67)* | **[US-33](./US-33-propuesta-asistida-formulario-c-segura-justa-apuesta.md)** (Propuesta Segura/Justa/Apuesta), **[US-34](./US-34-revision-inteligente-previa-confirmacion-formulario-c.md)** (Revisión pre-confirmación), **[US-35](./US-35-aviso-antecedentes-pendientes-impacto-puntaje.md)** (Aviso antecedentes pendientes), **[US-36](./US-36-recomendacion-vacantes-convenientes.md)** (Vacantes convenientes), **[US-37](./US-37-filtrado-escuelas-sugerencias-radio-viaje.md)** (Radio de viaje km), **[US-47](./US-47-postulacion-voluntaria-cobertura-vacante.md)** (Postulación a vacante), **[US-48](./US-48-emision-comprobante-pdf-postulacion-vacante.md)** (Comprobante postulación) |
| **`PD-78`** | Tarea | `PD-30` (Alertas) | Comportamiento notificación que al hacer clic te lleve a la ventana de inscripción | `validar` *(Implementado)* | **[US-46](./US-46-consulta-detalle-analitico-vacante.md)** (Detalle analítico de vacante y navegación a postulación), **[US-47](./US-47-postulacion-voluntaria-cobertura-vacante.md)** |
| **`PD-76`** | Historia | `PD-30` (Asistente) | US-MEJORA: Conectar con modelo vectorial / LLM el simulador y asistente | `Por hacer` *(Cerrado con Groq LLM)* | **[US-56](./US-56-interpretacion-lenguaje-natural-llm-groq.md)** (Interpretación de lenguaje natural mediante LLM Groq) |
| **`PD-79`** | Tarea | S/E | Pasar JSON a Supabase / PostgreSQL (Persistencia relacional ADR-028 + Espejo Junta ADR-029) | `Por hacer` *(Cerrado en PR #52/#74)* | **[US-64](./US-64-sincronizacion-escritura-espejo-tablas-junta.md)** (Escritura espejo Junta ADR-029), **[US-65](./US-65-presentacion-digital-reclamos-revision-lom.md)** (Reclamos LOM), **[US-66](./US-66-bandeja-resolucion-formal-reclamos-vocal.md)** (Resolución reclamos vocal), **[US-67](./US-67-consulta-historial-inscripciones-lom-ciclos-anteriores.md)** (Historial ciclos anteriores), **[US-68](./US-68-visualizacion-metricas-percentiles-competitividad-regional.md)** (Percentiles competitividad), **[US-69](./US-69-exportacion-reportes-operativos-gestion-vocales.md)** (Reportes vocales), **[US-76](./US-76-parametrizacion-ventana-convocatoria-ciclo-lectivo.md)** (Parametrización ventana convocatoria), **[US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md)** (Docker + PostgreSQL) |
| **`PD-61`** | Historia | `PD-22` (Auth) | US-01: Autenticación Real con OAuth CiDi + Gobernanza y Auditoría Inmutable (ADR-031 y ADR-032) | `Por hacer` *(Cerrado en PR #75/#76)* | **[US-01](./US-01-ingreso-redireccion-cidi.md)** (Redirección CiDi), **[US-02](./US-02-verificacion-cidi-nivel-2.md)** (CiDi Nivel 2), **[US-70](./US-70-registro-inmutable-eventos-acceso-sesion.md)** (Auditoría accesos), **[US-71](./US-71-bitacora-inmutable-operaciones-legajo-formulario-c.md)** (Bitácora legajo/Form C), **[US-72](./US-72-registro-auditable-dictamenes-validacion-rechazo-vocales.md)** (Auditoría dictámenes vocales), **[US-73](./US-73-consulta-filtrado-historial-auditoria-accesos-cambios.md)** (Consulta historial auditoría), **[US-74](./US-74-control-acceso-rbac-proteccion-secretos.md)** (RBAC y protección de secretos) |

---

## 4. Tabla Maestra: Las 80 Historias de Usuario (`US-01` a `US-80`) Conectadas a Jira y Sprints

| ID HU | Título Exacto de la Historia de Usuario | Épica Nueva | Épica Jira | Ticket(s) Jira (`PD-XX`) | Sprint(s) Jira | ID Histórico Repo / ADR | SP |
| :---: | :--- | :---: | :---: | :---: | :--- | :--- | :-: |
| **[US-01](./US-01-ingreso-redireccion-cidi.md)** | Ingreso por redirección desde Ciudadano Digital (CiDi) | E01 | `PD-22` | `PD-61`, `PD-35` | Sprint 1 / Sprint 12 | `US-01` / ADR-031 | 8 |
| **[US-02](./US-02-verificacion-cidi-nivel-2.md)** | Verificación obligatoria de Identidad CiDi Nivel 2 | E01 | `PD-22` | `PD-61` | Sprint 12 | `US-01` / ADR-031 | 3 |
| **[US-03](./US-03-bifurcacion-entorno-rol.md)** | Bifurcación automática de entorno según rol institucional (Aspirante / Vocal) | E01 | `PD-22` | `PD-35` | Sprint 1 | `US-01 (redirect)` / ADR-014 | 5 |
| **[US-04](./US-04-expiracion-inactividad-cierre-sesion.md)** | Expiración por inactividad y cierre seguro de sesión | E01 | `PD-22` | `PD-35`, `PD-61` | Sprint 1 / Sprint 12 | `US-01` / ADR-031 | 3 |
| **[US-05](./US-05-arnes-ingreso-simulado-temporal.md)** | Arnés de ingreso simulado temporal para entornos de desarrollo y homologación académica | E01 | `PD-22` / `PD-30` | `PD-35`, `PD-37` | Sprint 1 | `US-01 (Mock local)` | 3 |
| **[US-06](./US-06-panel-inicio-estado-ciclo.md)** | Panel de inicio con estado actual del ciclo administrativo docente | E02 | `PD-23` / `PD-30` | `PD-70` | Sprint 9 – Sprint 10 | `US-14 (Panel docente)` | 3 |
| **[US-07](./US-07-consulta-datos-personales-perfil.md)** | Consulta de datos personales e institucionales en Mi Perfil | E02 | `PD-23` | `PD-70` | Sprint 9 – Sprint 10 | `US-14` | 2 |
| **[US-08](./US-08-consulta-calculo-antiguedad-peoplenet.md)** | Consulta y cálculo de antigüedad docente desde registros provinciales (PeopleNet) | E02 | `PD-23` | `PD-70`, `PD-63` | Sprint 10 – Sprint 11 | `US-14` / PR #68 (PeopleNet) | 5 |
| **[US-09](./US-09-visualizacion-cargos-vigentes-trayectoria.md)** | Visualización de cargos vigentes e historial de trayectoria activa en el perfil | E02 | `PD-23` | `PD-65`, `PD-70` | Sprint 9 – Sprint 10 | `US-14` / ADR-013 | 5 |
| **[US-10](./US-10-configuracion-ubicacion-radio-traslado.md)** | Configuración de ubicación de referencia y radio de traslado preferido en el perfil | E02 | `PD-23` | `PD-70`, `PD-69` | Sprint 10 – Sprint 11 | `US-14` / PR #67 | 3 |
| **[US-11](./US-11-carga-manual-antecedentes-pdf.md)** | Carga manual de títulos y antecedentes externos con documento PDF probatorio | E03 | `PD-24` | `PD-38`, `PD-42`, `PD-79` | Sprint 2 / Sprint 3 / Sprint 11 | `US-03` (`US-02` Inst 2) / ADR-028 | 8 |
| **[US-12](./US-12-sincronizacion-automatica-antecedentes-sge.md)** | Sincronización automática de antecedentes y cursos desde registros provinciales (SGE / Red Provincial) | E03 | `PD-24` | `PD-38`, `PD-53` | Sprint 2 / Sprint 6 | `US-03 (sync)` / ADR-017 | 5 |
| **[US-13](./US-13-consulta-listado-estados-legajo.md)** | Consulta del listado del legajo y seguimiento de estados de validación | E03 | `PD-24` | `PD-38`, `PD-42` | Sprint 2 / Sprint 3 | `US-03` (`US-07` Inst 2) | 3 |
| **[US-14](./US-14-previsualizacion-descarga-certificado-pdf.md)** | Previsualización y descarga del certificado PDF adjunto en el legajo | E03 | `PD-24` | `PD-42` | Sprint 3 | `US-03 (visor PDF)` | 2 |
| **[US-15](./US-15-baja-antecedentes-manuales-pendientes.md)** | Baja o eliminación de antecedentes cargados manualmente en estado Pendiente | E03 | `PD-24` | `PD-38`, `PD-42` | Sprint 2 / Sprint 3 | `US-03` | 3 |
| **[US-16](./US-16-consulta-observaciones-motivos-rechazo.md)** | Consulta detallada de observaciones y motivos de rechazo emitidos por el Vocal | E03 | `PD-24` | `PD-42`, `PD-47` | Sprint 3 / Sprint 5 | `Inst 2 §4` (`US-04` Inst 2) | 3 |
| **[US-17](./US-17-consulta-puntaje-desglose-decreto-257.md)** | Consulta del puntaje total de inscripción y desglose por rubro normativo (Dto. 257/19) | E04 | `PD-27` | `PD-46`, `PD-56`, `PD-63`, `PD-67` | Sprint 4 / Sprint 7 / Sprint 11 | `US-05` / ADR-010 / PR #65 | 8 |
| **[US-18](./US-18-ponderacion-automatica-capacitaciones-titulos.md)** | Ponderación automática de capacitaciones y títulos según reglas del Decreto 257/19 | E04 | `PD-27` | `PD-63`, `PD-64`, `PD-67`, `PD-72` | Sprint 8 / Sprint 11 | `US-05` / ADR-012 / PR #65 | 5 |
| **[US-19](./US-19-consulta-puntaje-por-escuela-materia.md)** | Consulta de puntaje discriminado por escuela y espacio curricular inscripto | E04 | `PD-27` | `PD-46`, `PD-56` | Sprint 4 / Sprint 7 | `US-05` | 5 |
| **[US-20](./US-20-estimacion-automatica-puntaje-sin-valoracion-previa.md)** | Estimación automática de puntaje según Dto. 257/19 cuando el aspirante aún no registra valoración oficial en Junta | E04 | `PD-27` | `PD-46`, `PD-63` | Sprint 4 / Sprint 10–11 | `US-05` / ADR-027 | 5 |
| **[US-21](./US-21-distincion-lom-provisoria-definitiva.md)** | Distinción de estado de publicación de LOM (Provisoria vs. Definitiva) y ventana de exhibición | E04 | `PD-27` | `PD-56`, `PD-67` | Sprint 7 / Sprint 8 | `US-05` / ADR-010 | 3 |
| **[US-22](./US-22-consulta-catalogo-titulos-busqueda-competencias.md)** | Consulta del catálogo oficial de títulos y búsqueda de competencias habilitadas | E05 | `PD-26` | `PD-40`, `PD-44`, `PD-53` | Sprint 2 / Sprint 3 / Sprint 6 | `US-07` / ADR-009 | 5 |
| **[US-23](./US-23-validacion-tiempo-real-titulo-asignatura.md)** | Validación en tiempo real de compatibilidad Título–Asignatura durante la selección del Formulario C | E05 | `PD-26` | `PD-40`, `PD-44`, `PD-65`, `PD-72` | Sprint 2 / Sprint 3 / Sprint 10 | `US-07` | 5 |
| **[US-24](./US-24-busqueda-inversa-titulos-por-materia.md)** | Búsqueda inversa de títulos habilitantes por espacio curricular o cargo | E05 | `PD-26` | `PD-40`, `PD-53` | Sprint 2 / Sprint 6 | `US-07` / ADR-009 | 3 |
| **[US-25](./US-25-advertencia-preventiva-materia-sin-alcance.md)** | Advertencia preventiva al aspirante por selección de asignatura sin alcance de título | E05 | `PD-26` / `PD-25` | `PD-44`, `PD-65` | Sprint 3 / Sprint 10 | `US-07` + `US-04` | 3 |
| **[US-26](./US-26-marcado-automatico-sin-titulo-habilitante-vocal.md)** | Marcado automático de «Sin título habilitante» para revisión del Vocal de Junta | E05 | `PD-26` / `PD-28` | `PD-65`, `PD-66` | Sprint 9 – Sprint 10 (13/10) | `US-04` + `US-06` | 3 |
| **[US-27](./US-27-consulta-busqueda-padron-escuelas-oferta-curricular.md)** | Consulta y búsqueda del padrón oficial de escuelas secundarias y su oferta curricular | E06 | `PD-25` | `PD-43`, `PD-57`, `PD-71` | Sprint 3 / Sprint 7 / Sprint 10 | `US-04` / ADR-015 | 5 |
| **[US-28](./US-28-confeccion-formulario-c-tope-3x5.md)** | Confección del Formulario C con control estricto de tope reglamentario (3 escuelas × 5 materias/cargos) | E06 | `PD-25` | `PD-39`, `PD-43` | Sprint 2 (API) / Sprint 3 (UI) | `US-04` (`US-08` Inst 2) | 8 |
| **[US-29](./US-29-guardado-borrador-edicion-formulario-c.md)** | Guardado en borrador y edición del Formulario C durante la ventana de convocatoria | E06 | `PD-25` | `PD-39`, `PD-43`, `PD-79` | Sprint 2 / Sprint 3 / Sprint 11 | `US-04` / ADR-028 | 5 |
| **[US-30](./US-30-confirmacion-definitiva-formulario-c.md)** | Confirmación definitiva de la inscripción anual en el Formulario C | E06 | `PD-25` | `PD-39`, `PD-43` | Sprint 2 / Sprint 3 | `US-04` | 5 |
| **[US-31](./US-31-emision-descarga-comprobante-pdf-formulario-c.md)** | Emisión y descarga del comprobante digital PDF sellado del Formulario C | E06 | `PD-25` | `PD-43` | Sprint 3 | `US-04` | 3 |
| **[US-32](./US-32-bloqueo-modificacion-fuera-ventana-convocatoria.md)** | Bloqueo de alta y modificación del Formulario C fuera de la ventana de convocatoria | E06 | `PD-25` | `PD-39`, `PD-43` | Sprint 2 / Sprint 3 | `US-04` | 2 |
| **[US-33](./US-33-propuesta-asistida-formulario-c-segura-justa-apuesta.md)** | Propuesta asistida de Formulario C con clasificación estratégica de escuelas (Segura, Justa y Apuesta) | E07 | `PD-25` | `PD-69`, `PD-67` | Sprint 10 – Sprint 12 | `US-13` / PR #67 | 8 |
| **[US-34](./US-34-revision-inteligente-previa-confirmacion-formulario-c.md)** | Revisión inteligente previa a la confirmación del Formulario C (Alertas de riesgo y omisiones) | E07 | `PD-25` | `PD-69` | Sprint 10 – Sprint 12 | `US-13` / PR #67 | 5 |
| **[US-35](./US-35-aviso-antecedentes-pendientes-impacto-puntaje.md)** | Aviso de antecedentes pendientes de validación con impacto potencial en el puntaje de inscripción | E07 | `PD-25` | `PD-69`, `PD-63` | Sprint 10 – Sprint 12 | `US-13` / PR #67 | 3 |
| **[US-36](./US-36-recomendacion-vacantes-convenientes.md)** | Recomendación de vacantes activas que más convienen según puntaje, alcance y cercanía | E07 | `PD-25` | `PD-69` | Sprint 10 – Sprint 12 | `US-13` / PR #67 | 5 |
| **[US-37](./US-37-filtrado-escuelas-sugerencias-radio-viaje.md)** | Filtrado de escuelas y sugerencias por radio de viaje en kilómetros | E07 | `PD-25` | `PD-69`, `PD-70` | Sprint 10 – Sprint 12 | `US-13` / PR #67 | 3 |
| **[US-38](./US-38-visualizacion-mapa-interactivo-escuelas-departamentos.md)** | Visualización en mapa interactivo de establecimientos educativos y límites departamentales | E08 | `PD-29` | `PD-48`, `PD-55`, `PD-58` | Sprint 4 / Sprint 7 / Sprint 8–9 | `PD-21` / ADR-008 / ADR-016 | 8 |
| **[US-39](./US-39-semaforo-competitividad-lom-mapa.md)** | Semáforo de competitividad LOM en el mapa (Comparación de puntaje propio vs. promedio histórico) | E08 | `PD-29` | `PD-58`, `PD-67` | Sprint 8 – Sprint 9 | `PD-21` / ADR-019 | 8 |
| **[US-40](./US-40-agrupacion-sedes-madres-anexos-mapa.md)** | Agrupación jerárquica de Sedes Madres con sus Anexos y Extensiones Áulicas en el mapa | E08 | `PD-29` | `PD-58` | Sprint 9 (07/10) | `PD-21` / ADR-019 | 5 |
| **[US-41](./US-41-filtros-territoriales-curriculares-cascada-mapa.md)** | Filtros territoriales y curriculares en cascada sobre el mapa (Departamento, Localidad, Barrio, Materia y Semáforo) | E08 | `PD-29` | `PD-58` | Sprint 8 – Sprint 9 (07/10) | `PD-21` / ADR-019 | 5 |
| **[US-42](./US-42-visualizacion-vacantes-publicadas-junta-ficha-mapa.md)** | Visualización de vacantes publicadas por la Junta dentro de la ficha de escuela en el mapa | E08 | `PD-29` | `PD-55`, `PD-58` | Sprint 7 / Sprint 8 | `PD-21` / ADR-016 | 5 |
| **[US-43](./US-43-registro-baja-muestras-interes-mapa.md)** | Registro y baja de muestras de interés sobre escuelas y vacantes desde el mapa | E08 | `PD-29` | `PD-49` | Sprint 4 | `US-10 lite` | 3 |
| **[US-44](./US-44-bandeja-notificaciones-alertas-vacantes.md)** | Bandeja de notificaciones y alertas de nuevas vacantes compatibles con el perfil docente | E09 | `PD-30` | `PD-74` | Sprint 10 – Sprint 11 | `US-11 (Inst 2)` / ADR-020 | 5 |
| **[US-45](./US-45-filtrado-alertas-vacantes-formulario-c-intereses.md)** | Filtrado de alertas de vacantes por escuelas de mi Formulario C, intereses y departamento | E09 | `PD-30` | `PD-74` | Sprint 10 – Sprint 11 | `PD-74` / ADR-020 | 3 |
| **[US-46](./US-46-consulta-detalle-analitico-vacante.md)** | Consulta del detalle analítico de una solicitud de cobertura de vacante | E09 | `PD-30` | `PD-78` | Sprint 11 – Sprint 12 | `PD-78` | 2 |
| **[US-47](./US-47-postulacion-voluntaria-cobertura-vacante.md)** | Postulación voluntaria a solicitud de cobertura de vacante activa | E09 | `PD-30` / `PD-25` | `PD-69`, `PD-78` | Sprint 11 – Sprint 12 | `US-12 (Inst 2)` / `PD-69` | 5 |
| **[US-48](./US-48-emision-comprobante-pdf-postulacion-vacante.md)** | Emisión de comprobante digital inmutable de postulación a vacante | E09 | `PD-30` | `PD-69` | Sprint 11 – Sprint 12 | `PD-69` | 2 |
| **[US-49](./US-49-simulador-interactivo-puntaje-lom-sandbox.md)** | Simulador interactivo de puntaje LOM en entorno aislado (Sandbox Dto. 257/19) | E10 | `PD-30` / `PD-27` | `PD-50`, `PD-63` | Sprint 5 / Sprint 9–12 | `US-11` / ADR-011 | 5 |
| **[US-50](./US-50-proyeccion-escuelas-alcanzables-simulador-carrera.md)** | Proyección de escuelas alcanzables al incrementar el puntaje en el Simulador de Carrera | E10 | `PD-30` / `PD-27` | `PD-63`, `PD-67` | Sprint 9 – Sprint 12 | `US-11` / `PD-63` | 5 |
| **[US-51](./US-51-guardado-comparacion-3-escenarios-simulacion.md)** | Guardado y comparación de hasta 3 escenarios de simulación por usuario | E10 | `PD-30` | `PD-50`, `PD-63` | Sprint 5 / Sprint 11 | `US-11` | 3 |
| **[US-52](./US-52-recomendador-capacitaciones-catalogo-sipec.md)** | Recomendador de capacitaciones del catálogo tipo SIPEC ponderado según Decreto 257/19 | E10 | `PD-30` | `PD-50`, `PD-60`, `PD-64`, `PD-75`, `PD-77` | Sprint 5 / Sprint 8 / Sprint 10 | `US-12` / ADR-012 | 5 |
| **[US-53](./US-53-identificacion-rubros-margen-mejora-puntaje.md)** | Identificación visual de rubros con mayor margen de mejora en el perfil de puntaje | E10 | `PD-30` / `PD-27` | `PD-63`, `PD-64` | Sprint 9 – Sprint 12 | `US-11` / Dto. 257/19 | 2 |
| **[US-54](./US-54-interfaz-conversacion-sugerencias-asistente-virtual.md)** | Interfaz de conversación y sugerencias rápidas del Asistente Virtual Docente | E11 | `PD-30` | `PD-73` | Sprint 10 – Sprint 12 | `PD-73 (Chat-bot)` | 5 |
| **[US-55](./US-55-respuestas-contextualizadas-datos-reales-asistente.md)** | Respuestas contextualizadas con los datos reales del docente en el Asistente Virtual | E11 | `PD-30` | `PD-73`, `PD-70` | Sprint 10 – Sprint 12 | `PD-73` | 5 |
| **[US-56](./US-56-interpretacion-lenguaje-natural-llm-groq.md)** | Interpretación de lenguaje natural mediante modelo de lenguaje (LLM Groq) en el Asistente | E11 | `PD-30` | `PD-73`, `PD-76` | Sprint 10 – Sprint 12 | `PD-73` / `PD-76` | 5 |
| **[US-57](./US-57-contingencia-deterministica-local-asistente.md)** | Mecanismo de contingencia determinístico local ante falta de clave o caída del servicio LLM | E11 | `PD-30` | `PD-73` | Sprint 10 – Sprint 12 | `PD-73` | 3 |
| **[US-58](./US-58-resguardo-datos-personales-pii-consultas-llm.md)** | Resguardo de datos personales identificatorios (PII) en consultas al modelo de lenguaje externo | E11 | `PD-30` | `PD-73` | Sprint 11 – Sprint 12 | `PD-73` | 2 |
| **[US-59](./US-59-bandeja-establecimientos-vocal-aislamiento-jurisdiccion.md)** | Bandeja de establecimientos asignados al Vocal con aislamiento estricto de jurisdicción | E12 | `PD-28` | `PD-47`, `PD-66` | Sprint 5 / Sprint 8 | `US-06` / ADR-018 | 5 |
| **[US-60](./US-60-listado-busqueda-filtrado-aspirantes-por-escuela.md)** | Listado, búsqueda y filtrado de aspirantes por escuela y estado de revisión | E12 | `PD-28` | `PD-47`, `PD-66` | Sprint 5 / Sprint 8 | `US-06` | 5 |
| **[US-61](./US-61-inspeccion-integral-lupa-legajo-inscripcion-vocal.md)** | Inspección integral (Lupa) del legajo e inscripción del aspirante por parte del Vocal | E12 | `PD-28` | `PD-47`, `PD-66`, `PD-65` | Sprint 5 / Sprint 8 / Sprint 10 | `US-06` | 5 |
| **[US-62](./US-62-aprobacion-aceptacion-antecedentes-vocal.md)** | Aprobación (Aceptación) de antecedentes del legajo por el Vocal de Junta | E12 | `PD-28` | `PD-47`, `PD-79` | Sprint 5 / Sprint 11 | `US-06` / ADR-028 | 3 |
| **[US-63](./US-63-rechazo-fundado-antecedentes-motivo-obligatorio-vocal.md)** | Rechazo fundado de antecedentes con motivo obligatorio por el Vocal de Junta | E12 | `PD-28` | `PD-47`, `PD-79` | Sprint 5 / Sprint 11 | `US-06` / `Inst 2 §4` | 5 |
| **[US-64](./US-64-sincronizacion-escritura-espejo-tablas-junta.md)** | Sincronización y escritura espejo en las tablas de la Junta en entornos de desarrollo y prueba | E12 | `PD-28` | `PD-79` | Sprint 11 – Sprint 12 | ADR-029 / PR #74 | 5 |
| **[US-65](./US-65-presentacion-digital-reclamos-revision-lom.md)** | Presentación digital de reclamos de revisión de LOM o antecedentes por el aspirante | E13 | `PD-28` / `PD-27` | `PD-63`, `PD-79` | Sprint 11 – Sprint 12 | Evolución Junta / LOM | 5 |
| **[US-66](./US-66-bandeja-resolucion-formal-reclamos-vocal.md)** | Bandeja de resolución formal de reclamos para Vocales de la Junta | E13 | `PD-28` | `PD-66`, `PD-79` | Sprint 11 – Sprint 12 | Evolución Junta / LOM | 5 |
| **[US-67](./US-67-consulta-historial-inscripciones-lom-ciclos-anteriores.md)** | Consulta de historial de inscripciones, postulaciones y resultados de LOM de ciclos anteriores | E13 | `PD-27` | `PD-56`, `PD-67` | Sprint 7 – Sprint 9 | `US-05` / ADR-019 | 3 |
| **[US-68](./US-68-visualizacion-metricas-percentiles-competitividad-regional.md)** | Visualización de métricas y percentiles anonimizados de competitividad regional | E13 | `PD-27` / `PD-29` | `PD-67`, `PD-69` | Sprint 8 – Sprint 11 | `US-05` / ADR-019 | 5 |
| **[US-69](./US-69-exportacion-reportes-operativos-gestion-vocales.md)** | Exportación de reportes operativos de gestión para Vocales de la Junta | E13 | `PD-28` | `PD-66`, `PD-79` | Sprint 8 / Sprint 11 | `US-06` (Reportes Junta) | 3 |
| **[US-70](./US-70-registro-inmutable-eventos-acceso-sesion.md)** | Registro inmutable de eventos de acceso y sesión conforme a estándares institucionales | E14 | `PD-28` / `PD-22` | `PD-61`, `PD-79` | Sprint 11 – Sprint 12 | ADR-032 / PR #76 | 3 |
| **[US-71](./US-71-bitacora-inmutable-operaciones-legajo-formulario-c.md)** | Bitácora inmutable de operaciones sobre el legajo digital y el Formulario C | E14 | `PD-28` / `PD-24` | `PD-38`, `PD-79` | Sprint 11 – Sprint 12 | ADR-028 / ADR-032 | 5 |
| **[US-72](./US-72-registro-auditable-dictamenes-validacion-rechazo-vocales.md)** | Registro auditable de dictámenes de validación y rechazo emitidos por Vocales | E14 | `PD-28` | `PD-47`, `PD-79` | Sprint 11 – Sprint 12 | ADR-028 / ADR-032 | 3 |
| **[US-73](./US-73-consulta-filtrado-historial-auditoria-accesos-cambios.md)** | Consulta y filtrado del historial de auditoría de accesos y cambios | E14 | `PD-28` | `PD-79` | Sprint 12 | ADR-032 / PR #76 | 5 |
| **[US-74](./US-74-control-acceso-rbac-proteccion-secretos.md)** | Control de acceso basado en roles (RBAC) y protección de cabeceras y secretos | E14 | `PD-22` | `PD-35`, `PD-61` | Sprint 1 / Sprint 12 | ADR-014 / ADR-031 / ADR-032 | 5 |
| **[US-75](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md)** | Conmutación transparente de modo de integración (Oracle Lab Productivo vs. Modo Mock) y rótulos de transparencia | S/E | S/E | `PD-37`, `PD-41`, `PD-45`, `PD-52` | Sprint 1 / Sprint 2 / Sprint 6 / Sprint 11 | ADR-009 / `DATOS_REALES_Y_SIMULADOS.md` | 5 |
| **[US-76](./US-76-parametrizacion-ventana-convocatoria-ciclo-lectivo.md)** | Parametrización administrativa de ventana de convocatoria anual y ciclo lectivo | S/E | S/E | `PD-39`, `PD-79` | Sprint 2 / Sprint 11 | Configuración convocatoria | 3 |
| **[US-77](./US-77-monitoreo-salud-operativa-conectividad-health.md)** | Monitoreo de salud operativa y diagnóstico de conectividad a bases de datos (`/api/health`) | S/E | S/E | `PD-36`, `PD-45` | Sprint 1 / Sprint 6 | `/api/health` | 2 |
| **[US-78](./US-78-resiliencia-degradacion-elegante-servicios-externos.md)** | Resiliencia y degradación elegante ante intermitencias de servicios externos o VPN | S/E | S/E | `PD-45`, `PD-51`, `PD-54` | Sprint 5 / Sprint 6 | ADR-009 / `ORA-00942` | 5 |
| **[US-79](./US-79-diseno-adaptable-responsive-accesibilidad-web.md)** | Diseño adaptable (Responsive) y criterios de accesibilidad web para docentes y vocales | S/E | S/E | `PD-42`, `PD-58`, `PD-66` | Sprint 3 / Sprint 8 | UI Responsive Angular | 5 |
| **[US-80](./US-80-despliegue-contenedores-docker-inicializacion-bd.md)** | Despliegue reproducible en contenedores Docker e inicialización automática de esquemas y datos semilla | S/E | S/E | `PD-32`, `PD-34`, `PD-36`, `PD-79` | Sprint 0 / Sprint 1 / Sprint 11 | Docker + PostgreSQL + Seeds | 5 |

---

## 5. Mapa Inverso Rápido: Ticket Jira (`PD-31` a `PD-79`) → Nuevas HUs (`US-01` a `US-80`)

| Clave Jira | Resumen en Jira | Sprint en Jira | HUs del Nuevo Catálogo (`US-01`–`US-80`) |
| :--- | :--- | :--- | :--- |
| **`PD-31`** | Definición de Working Agreement y roles | `PD Sprint 0` | Organización del equipo G24 |
| **`PD-32`** | Creación de política de ramas y PR | `PD Sprint 0` | `US-80` |
| **`PD-33`** | Diagramas de Arquitectura (C4) | `PD Sprint 0` | `US-75`, `US-78` |
| **`PD-34`** | Creación de repositorio base en GitHub | `PD Sprint 0` | `US-80` |
| **`PD-35`** | US-01: Autenticarse (Fase 1: Mock local) | `PD Sprint 1` | `US-03`, `US-04`, `US-05`, `US-74` |
| **`PD-36`** | Configurar monorepo y pipeline CI | `PD Sprint 1` | `US-77`, `US-80` |
| **`PD-37`** | Creación de fixtures de usuarios demo | `PD Sprint 1` | `US-05`, `US-75` |
| **`PD-38`** | US-03: Actualizar legajo y antecedentes (Backend API) | `PD Sprint 2` | `US-11`, `US-12`, `US-13`, `US-15`, `US-71` |
| **`PD-39`** | US-04: Completar Formulario C (Reglas de negocio) | `PD Sprint 2` | `US-28`, `US-29`, `US-30`, `US-32`, `US-76` |
| **`PD-40`** | US-07: Validar título (Habilitación por asignatura API) | `PD Sprint 2` | `US-22`, `US-23`, `US-24` |
| **`PD-41`** | Implementación modo offline (SQL) | `PD Sprint 2` | `US-75` |
| **`PD-42`** | US-03: UI Pantallas de carga de legajo y adjuntos | `PD Sprint 3` | `US-11`, `US-13`, `US-14`, `US-15`, `US-16`, `US-79` |
| **`PD-43`** | US-04: UI Pantalla Formulario C con catálogo | `PD Sprint 3` | `US-27`, `US-28`, `US-29`, `US-30`, `US-31`, `US-32` |
| **`PD-44`** | US-07: UI Integración validador en interfaz | `PD Sprint 3` | `US-22`, `US-23`, `US-25` |
| **`PD-45`** | Gestionar credenciales y conexión a Oracle Lab | `PD Sprint 6` (Rollover 3→6) | `US-75`, `US-77`, `US-78` |
| **`PD-46`** | US-05: Consultar puntaje de inscripción (Mock) | `PD Sprint 4` | `US-17`, `US-19`, `US-20` |
| **`PD-47`** | US-06: Revisar aspirantes por escuela (UI Cruda y Bugs) | `PD Sprint 5` | `US-16`, `US-59`, `US-60`, `US-61`, `US-62`, `US-63`, `US-72` |
| **`PD-48`** | PD-21: Mapa de vacantes territorial (Leaflet mock) | `PD Sprint 4` | `US-38` |
| **`PD-49`** | US-10 lite: Registrar interés desde mapa | `PD Sprint 4` | `US-43` |
| **`PD-50`** | US-11 / US-12: Simulador de ascenso y capacitaciones | `PD Sprint 5` | `US-49`, `US-51`, `US-52` |
| **`PD-51`** | Configuración VPN OpenVPN del Ministerio | `PD Sprint 5` | `US-75`, `US-78` |
| **`PD-52`** | Alineación de fixtures demo a nombres del dump | `PD Sprint 5` | `US-75`, `US-80` |
| **`PD-53`** | US-07: Conexión Validador alcance a Oracle | `PD Sprint 6` | `US-12`, `US-22`, `US-24` |
| **`PD-54`** | Refactor por problema ORA-00942 | `PD Sprint 6` | `US-78` |
| **`PD-55`** | PD-21: Integración Mapa vacantes con Oracle | `PD Sprint 7` | `US-38`, `US-42` |
| **`PD-56`** | US-05: Integración Puntaje LOM parcial | `PD Sprint 7` | `US-17`, `US-19`, `US-21`, `US-67` |
| **`PD-57`** | US-04: Conexión Padrón y catálogo Form C a Oracle | `PD Sprint 7` | `US-27` |
| **`PD-58`** | PD-21 / US-10: Mejoras estéticas de UI/UX en Mapa | `PD Sprint 8` – `9` | `US-38`, `US-39`, `US-40`, `US-41`, `US-42`, `US-79` |
| **`PD-60`** | US-12: Conexión con listado de Cursos de Capacitación | `PD Sprint 8` | `US-52` |
| **`PD-61`** | US-01: Autenticación Real con OAuth CiDi | `PD Sprint 12` (Backlog→Cierre) | `US-01`, `US-02`, `US-04`, `US-70`, `US-74` |
| **`PD-63`** | US-05: SIMULADOR DE AVANCE DE CARRERA (Desglose LOM) | `PD Sprint 9` a `12` | `US-08`, `US-17`, `US-18`, `US-20`, `US-35`, `US-49`, `US-50`, `US-51`, `US-53`, `US-65` |
| **`PD-64`** | Obtener información de cuánto puntaje da cada curso | `PD Sprint 8` | `US-18`, `US-52`, `US-53` |
| **`PD-65`** | Comportamiento de alcance título e inscripción como supletorio / escuelas base | `PD Sprint 9` – `10` | `US-09`, `US-23`, `US-25`, `US-26`, `US-61` |
| **`PD-66`** | Mejora de UI/UX en bandeja de vocales | `PD Sprint 8` | `US-26`, `US-59`, `US-60`, `US-61`, `US-66`, `US-69`, `US-79` |
| **`PD-67`** | Spike funcionamiento del cálculo de LOM actual | `PD Sprint 8` | `US-17`, `US-18`, `US-21`, `US-33`, `US-39`, `US-50`, `US-67`, `US-68` |
| **`PD-69`** | US-13: Conexión de postulación de cargos con inscripciones del docente | `PD Sprint 8` a `12` | `US-10`, `US-33`, `US-34`, `US-35`, `US-36`, `US-37`, `US-47`, `US-48`, `US-68` |
| **`PD-70`** | US-14: Panel de docente | `PD Sprint 9` a `12` | `US-06`, `US-07`, `US-08`, `US-09`, `US-10`, `US-37`, `US-55` |
| **`PD-71`** | US-MEJORA: Filtros de búsqueda para materias del plan de estudio | `PD Sprint 10` a `12` | `US-27` |
| **`PD-72`** | Relacionar vista de materias por título con nivel educativo / carácter | `PD Sprint 10` a `12` | `US-18`, `US-22`, `US-23` |
| **`PD-73`** | US: Desarrollar un chat-bot | `PD Sprint 10` a `12` | `US-54`, `US-55`, `US-56`, `US-57`, `US-58` |
| **`PD-74`** | US: Notificador de postulación | `PD Sprint 10` a `12` | `US-44`, `US-45` |
| **`PD-75`** | Filtrar listado de cursos por localidad y materias habilitadas | `PD Sprint 10` | `US-52` |
| **`PD-76`** | US-MEJORA: Conectar con modelo vectorial / LLM el simulador y asistente | `PD Sprint 11` – `12` | `US-56` |
| **`PD-77`** | US-MEJORA: Filtros para cursos | `PD Sprint 10` | `US-52` |
| **`PD-78`** | Comportamiento notificación que al hacer clic lleve a inscripción | `PD Sprint 11` – `12` | `US-46`, `US-47` |
| **`PD-79`** | Pasar JSON a Supabase / PostgreSQL | `PD Sprint 11` – `12` | `US-11`, `US-29`, `US-62`, `US-63`, `US-64`, `US-65`, `US-66`, `US-69`, `US-70`, `US-71`, `US-72`, `US-73`, `US-76`, `US-80` |

# Architecture Decision Records (ADR)

Decisiones técnicas del portal G24. Nombre: `ADR-<NNN>-<titulo-corto>.md`.

| ID | Archivo | Tema |
|----|---------|------|
| 001 | [ADR-001-frontend-angular-ssr.md](./ADR-001-frontend-angular-ssr.md) | Frontend Angular SSR |
| 002 | [ADR-002-backend-node-typescript.md](./ADR-002-backend-node-typescript.md) | Backend Node + TS |
| 003 | [ADR-003-base-datos-oracle.md](./ADR-003-base-datos-oracle.md) | Oracle |
| 004 | [ADR-004-monolito-modular-capas.md](./ADR-004-monolito-modular-capas.md) | Monolito modular |
| 005 | [ADR-005-autenticacion-cidi.md](./ADR-005-autenticacion-cidi.md) | CiDi / OAuth2 / JWT |
| 006 | [ADR-006-api-rest.md](./ADR-006-api-rest.md) | API REST |
| 007 | [ADR-007-ci-github-actions.md](./ADR-007-ci-github-actions.md) | CI GitHub Actions |
| 008 | [ADR-008-mapas-leaflet.md](./ADR-008-mapas-leaflet.md) | Mapas Leaflet (spike PD-21; modo IDECOR quitado el 2026-10-07) |
| 009 | [ADR-009-integracion-alcance-titulo.md](./ADR-009-integracion-alcance-titulo.md) | Alcance título / validador US-07 |
| 010 | [ADR-010-motor-lom.md](./ADR-010-motor-lom.md) | Motor LOM — contrato US-05 |
| 011 | [ADR-011-simulador-ascenso.md](./ADR-011-simulador-ascenso.md) | Simulador ascenso (mock US-11) |
| 012 | [ADR-012-recomendador-capacitacion.md](./ADR-012-recomendador-capacitacion.md) | Recomendador capacitaciones (mock US-12) |
| 013 | [ADR-013-escuela-base-deprecacion.md](./ADR-013-escuela-base-deprecacion.md) | Escuela base — sin paso US-02 (aceptado) |
| 014 | [ADR-014-roles-aspirante-vocal-p2.md](./ADR-014-roles-aspirante-vocal-p2.md) | Roles P2 — US-05 / US-06 |
| 015 | [ADR-015-padron-oracle-lab.md](./ADR-015-padron-oracle-lab.md) | Padrón + catálogo Form C Oracle lab |
| 016 | [ADR-016-mapa-vacantes-oracle-lab.md](./ADR-016-mapa-vacantes-oracle-lab.md) | Mapa vacantes Oracle lab |
| 017 | [ADR-017-sge-legajo-oracle-lab-pendiente.md](./ADR-017-sge-legajo-oracle-lab-pendiente.md) | SGE legajo — bloqueado lab (pendiente JTP) |
| 018 | [ADR-018-vocal-junta-oracle-lab-pendiente.md](./ADR-018-vocal-junta-oracle-lab-pendiente.md) | Vocal × escuela — bloqueado lab (pendiente JTP) |
| 019 | [ADR-019-lom-secundaria-mapa-oracle-lab.md](./ADR-019-lom-secundaria-mapa-oracle-lab.md) | Promedio LOM secundaria (top 5 docentes por escuela, solo `T_DETALLES_INSC_JD`) — semáforo mapa (datos históricos) |
| 020 | [ADR-020-alertas-vacantes-mock.md](./ADR-020-alertas-vacantes-mock.md) | Alertas de vacantes (mock demo; en prod `T_PUBLICACIONES` vía `VT_PUBLI_EMPRE_MAT_ASPIRANTES`) |
| 021 | [ADR-021-contenedores-docker.md](./ADR-021-contenedores-docker.md) | Contenerización con Docker y Docker Compose |
| 022 | [ADR-022-asistente-docente-propio.md](./ADR-022-asistente-docente-propio.md) | Asistente docente (chatbot) propio del portal |
| 023 | [ADR-023-asistente-con-llm.md](./ADR-023-asistente-con-llm.md) | Asistente docente con modelo de lenguaje (Groq) como intérprete del texto libre |
| 024 | [ADR-024-simulador-carrera-docente.md](./ADR-024-simulador-carrera-docente.md) | Simulador de carrera docente: cursos, años y títulos hacia las escuelas a las que llega |
| 025 | [ADR-025-ayudas-para-decidir.md](./ADR-025-ayudas-para-decidir.md) | Ayudas para decidir: propuesta y revisión del Formulario C, antecedentes pendientes, vacantes que convienen y radio de viaje |
| 026 | [ADR-026-datos-lab-por-defecto.md](./ADR-026-datos-lab-por-defecto.md) | Datos del lab Oracle por defecto; mock como alternativa |
| 027 | [ADR-027-valoracion-decreto-257-19.md](./ADR-027-valoracion-decreto-257-19.md) | Valoración de títulos y antecedentes según el Dto. 257/19 (reemplaza el baremo provisorio) |
| 028 | [ADR-028-persistencia-postgresql.md](./ADR-028-persistencia-postgresql.md) | Persistencia de los datos del portal en PostgreSQL detrás de una interfaz (lab Oracle de solo lectura) |
| 029 | [ADR-029-escritura-tablas-junta.md](./ADR-029-escritura-tablas-junta.md) | Escritura en las tablas de la Junta en desarrollo y prueba (espejo del legajo y del Formulario C, lista cerrada) |
| 030 | [ADR-030-antiguedad-docente-perfil.md](./ADR-030-antiguedad-docente-perfil.md) | Antigüedad docente, cargos vigentes y trayectoria en el perfil (PEOPLENET.M4CBA_VW_CONC_DIR + M4CBA_VW_CONSE_CAR) |
| 031 | [ADR-031-ingreso-cidi.md](./ADR-031-ingreso-cidi.md) | Ingreso con Ciudadano Digital (nivel 2, rol de vocal y cierre por inactividad) |
| 032 | [ADR-032-auditoria-accesos-cambios.md](./ADR-032-auditoria-accesos-cambios.md) | Auditoría de accesos y cambios en PostgreSQL, solo agregar registros |

Origen: Instancia 2 — Sprint 0 (G24). Mantener alineados con la tabla ADR del documento de entrega y con `SCM.md`.

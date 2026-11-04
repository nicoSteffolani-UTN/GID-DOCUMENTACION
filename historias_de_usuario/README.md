# Catálogo Maestro de Épicas e Historias de Usuario — Proyecto GID (Grupo 24)

**Proyecto:** Gestión Integral Docente (GID) — Portal Provincial de Gestión Docente Secundaria  
**Organización Destinataria:** Ministerio de Educación de la Provincia de Córdoba — Dirección General de Carrera Docente / Junta de Clasificación Secundaria  
**Cátedra:** Proyecto Final — Curso 5K3 — UTN Facultad Regional Córdoba  

**Documentos Maestros de Trazabilidad:**
- **[Matriz de Trazabilidad Jira (`PD`) ↔ Sprints (`0` a `12`) ↔ 80 HUs](./TRAZABILIDAD_JIRA_SPRINTS_HUS.md)**
- **[Matriz Maestra de Casos de Prueba (`US-01` a `US-80`)](./MATRIZ_CASOS_DE_PRUEBA.md)**

---

## Resumen de Épicas (14 Épicas + Bloque Transversal Sin Épica = 80 HUs)

| Código | Nombre de la Épica | Rango de HUs | Cantidad | SP Totales |
| :---: | :--- | :---: | :---: | :---: |
| **E01** | Identidad Digital, Autenticación y Sesión (CiDi) | US-01 a US-05 | 5 HUs | 22 SP |
| **E02** | Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet) | US-06 a US-10 | 5 HUs | 18 SP |
| **E03** | Gestión del Legajo Digital y Antecedentes | US-11 a US-16 | 6 HUs | 24 SP |
| **E04** | Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19) | US-17 a US-21 | 5 HUs | 26 SP |
| **E05** | Validador de Alcance de Títulos y Competencias | US-22 a US-26 | 5 HUs | 19 SP |
| **E06** | Convocatoria Anual e Inscripción en Formulario C | US-27 a US-32 | 6 HUs | 28 SP |
| **E07** | Ayudas a la Decisión y Recomendación de Postulación | US-33 a US-37 | 5 HUs | 24 SP |
| **E08** | Mapa Territorial Interactivo y Semáforo LOM | US-38 a US-43 | 6 HUs | 34 SP |
| **E09** | Alertas y Cobertura de Vacantes Activas | US-44 a US-48 | 5 HUs | 17 SP |
| **E10** | Simulador de Carrera Docente y Recomendador SIPEC | US-49 a US-53 | 5 HUs | 20 SP |
| **E11** | Asistente Virtual Docente con IA (Chatbot + LLM) | US-54 a US-58 | 5 HUs | 20 SP |
| **E12** | Gestión y Fiscalización para Vocales de Junta | US-59 a US-64 | 6 HUs | 28 SP |
| **E13** | Gestión de Reclamos, Historial LOM y Analítica | US-65 a US-69 | 5 HUs | 21 SP |
| **E14** | Gobernanza, Seguridad y Auditoría Inmutable | US-70 a US-74 | 5 HUs | 21 SP |
| **S/E** | Historias sin Épica (Transversales, Operación e Interoperabilidad) | US-75 a US-80 | 6 HUs | 25 SP |
| **TOTAL** | **14 Épicas + 1 Bloque Sin Épica** | **US-01 a US-80** | **80 HUs** | **347 SP** |

---

## Índice Completo de Historias de Usuario

### Épica E01: Identidad Digital, Autenticación y Sesión (CiDi)
1. [US-01 — Ingreso por redirección desde Ciudadano Digital (CiDi)](./US-01-ingreso-redireccion-cidi.md) *(8 SP - P1)*
2. [US-02 — Verificación obligatoria de Identidad CiDi Nivel 2](./US-02-verificacion-cidi-nivel-2.md) *(3 SP - P1)*
3. [US-03 — Bifurcación automática de entorno según rol institucional (Aspirante / Vocal)](./US-03-bifurcacion-entorno-rol.md) *(5 SP - P1)*
4. [US-04 — Expiración por inactividad y cierre seguro de sesión](./US-04-expiracion-inactividad-cierre-sesion.md) *(3 SP - P1)*
5. [US-05 — Arnés de ingreso simulado temporal para entornos de desarrollo y homologación académica](./US-05-arnes-ingreso-simulado-temporal.md) *(3 SP - P1)*

### Épica E02: Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)
6. [US-06 — Panel de inicio con estado actual del ciclo administrativo docente](./US-06-panel-inicio-estado-ciclo.md) *(3 SP - P1)*
7. [US-07 — Consulta de datos personales e institucionales en Mi Perfil](./US-07-consulta-datos-personales-perfil.md) *(2 SP - P1)*
8. [US-08 — Consulta y cálculo de antigüedad docente desde registros provinciales (PeopleNet)](./US-08-consulta-calculo-antiguedad-peoplenet.md) *(5 SP - P2)*
9. [US-09 — Visualización de cargos vigentes e historial de trayectoria activa en el perfil](./US-09-visualizacion-cargos-vigentes-trayectoria.md) *(5 SP - P2)*
10. [US-10 — Configuración de ubicación de referencia y radio de traslado preferido en el perfil](./US-10-configuracion-ubicacion-radio-traslado.md) *(3 SP - P2)*

### Épica E03: Gestión del Legajo Digital y Antecedentes
11. [US-11 — Carga manual de títulos y antecedentes externos con documento PDF probatorio](./US-11-carga-manual-antecedentes-pdf.md) *(8 SP - P1)*
12. [US-12 — Sincronización automática de antecedentes y cursos desde registros provinciales (SGE / Red Provincial)](./US-12-sincronizacion-automatica-antecedentes-sge.md) *(5 SP - P1)*
13. [US-13 — Consulta del listado del legajo y seguimiento de estados de validación](./US-13-consulta-listado-estados-legajo.md) *(3 SP - P1)*
14. [US-14 — Previsualización y descarga del certificado PDF adjunto en el legajo](./US-14-previsualizacion-descarga-certificado-pdf.md) *(2 SP - P2)*
15. [US-15 — Baja o eliminación de antecedentes cargados manualmente en estado Pendiente](./US-15-baja-antecedentes-manuales-pendientes.md) *(3 SP - P2)*
16. [US-16 — Consulta detallada de observaciones y motivos de rechazo emitidos por el Vocal](./US-16-consulta-observaciones-motivos-rechazo.md) *(3 SP - P1)*

### Épica E04: Motor de Valoración Reglamentaria y Puntaje (Dto. 257/19)
17. [US-17 — Consulta del puntaje total de inscripción y desglose por rubro normativo (Dto. 257/19)](./US-17-consulta-puntaje-desglose-decreto-257.md) *(8 SP - P1)*
18. [US-18 — Ponderación automática de capacitaciones y títulos según reglas del Decreto 257/19](./US-18-ponderacion-automatica-capacitaciones-titulos.md) *(5 SP - P1)*
19. [US-19 — Consulta de puntaje discriminado por escuela y espacio curricular inscripto](./US-19-consulta-puntaje-por-escuela-materia.md) *(5 SP - P1)*
20. [US-20 — Estimación automática de puntaje según Dto. 257/19 cuando el aspirante aún no registra valoración oficial en Junta](./US-20-estimacion-automatica-puntaje-sin-valoracion-previa.md) *(5 SP - P1)*
21. [US-21 — Distinción de estado de publicación de LOM (Provisoria vs. Definitiva) y ventana de exhibición](./US-21-distincion-lom-provisoria-definitiva.md) *(3 SP - P2)*

### Épica E05: Validador de Alcance de Títulos y Competencias
22. [US-22 — Consulta del catálogo oficial de títulos y búsqueda de competencias habilitadas](./US-22-consulta-catalogo-titulos-busqueda-competencias.md) *(5 SP - P1)*
23. [US-23 — Validación en tiempo real de compatibilidad Título–Asignatura durante la selección del Formulario C](./US-23-validacion-tiempo-real-titulo-asignatura.md) *(5 SP - P1)*
24. [US-24 — Búsqueda inversa de títulos habilitantes por espacio curricular o cargo](./US-24-busqueda-inversa-titulos-por-materia.md) *(3 SP - P2)*
25. [US-25 — Advertencia preventiva al aspirante por selección de asignatura sin alcance de título](./US-25-advertencia-preventiva-materia-sin-alcance.md) *(3 SP - P1)*
26. [US-26 — Marcado automático de «Sin título habilitante» para revisión del Vocal de Junta](./US-26-marcado-automatico-sin-titulo-habilitante-vocal.md) *(3 SP - P1)*

### Épica E06: Convocatoria Anual e Inscripción en Formulario C
27. [US-27 — Consulta y búsqueda del padrón oficial de escuelas secundarias y su oferta curricular](./US-27-consulta-busqueda-padron-escuelas-oferta-curricular.md) *(5 SP - P1)*
28. [US-28 — Confección del Formulario C con control estricto de tope reglamentario (3 escuelas × 5 materias/cargos)](./US-28-confeccion-formulario-c-tope-3x5.md) *(8 SP - P1)*
29. [US-29 — Guardado en borrador y edición del Formulario C durante la ventana de convocatoria](./US-29-guardado-borrador-edicion-formulario-c.md) *(5 SP - P1)*
30. [US-30 — Confirmación definitiva de la inscripción anual en el Formulario C](./US-30-confirmacion-definitiva-formulario-c.md) *(5 SP - P1)*
31. [US-31 — Emisión y descarga del comprobante digital PDF sellado del Formulario C](./US-31-emision-descarga-comprobante-pdf-formulario-c.md) *(3 SP - P1)*
32. [US-32 — Bloqueo de alta y modificación del Formulario C fuera de la ventana de convocatoria](./US-32-bloqueo-modificacion-fuera-ventana-convocatoria.md) *(2 SP - P1)*

### Épica E07: Ayudas a la Decisión y Recomendación de Postulación
33. [US-33 — Propuesta asistida de Formulario C con clasificación estratégica de escuelas (Segura, Justa y Apuesta)](./US-33-propuesta-asistida-formulario-c-segura-justa-apuesta.md) *(8 SP - P1)*
34. [US-34 — Revisión inteligente previa a la confirmación del Formulario C (Alertas de riesgo y omisiones)](./US-34-revision-inteligente-previa-confirmacion-formulario-c.md) *(5 SP - P1)*
35. [US-35 — Aviso de antecedentes pendientes de validación con impacto potencial en el puntaje de inscripción](./US-35-aviso-antecedentes-pendientes-impacto-puntaje.md) *(3 SP - P2)*
36. [US-36 — Recomendación de vacantes activas que más convienen según puntaje, alcance y cercanía](./US-36-recomendacion-vacantes-convenientes.md) *(5 SP - P2)*
37. [US-37 — Filtrado de escuelas y sugerencias por radio de viaje en kilómetros](./US-37-filtrado-escuelas-sugerencias-radio-viaje.md) *(3 SP - P2)*

### Épica E08: Mapa Territorial Interactivo y Semáforo LOM
38. [US-38 — Visualización en mapa interactivo de establecimientos educativos y límites departamentales](./US-38-visualizacion-mapa-interactivo-escuelas-departamentos.md) *(8 SP - P1)*
39. [US-39 — Semáforo de competitividad LOM en el mapa (Comparación de puntaje propio vs. promedio histórico)](./US-39-semaforo-competitividad-lom-mapa.md) *(8 SP - P1)*
40. [US-40 — Agrupación jerárquica de Sedes Madres con sus Anexos y Extensiones Áulicas en el mapa](./US-40-agrupacion-sedes-madres-anexos-mapa.md) *(5 SP - P2)*
41. [US-41 — Filtros territoriales y curriculares en cascada sobre el mapa (Departamento, Localidad, Barrio, Materia y Semáforo)](./US-41-filtros-territoriales-curriculares-cascada-mapa.md) *(5 SP - P1)*
42. [US-42 — Visualización de vacantes publicadas por la Junta dentro de la ficha de escuela en el mapa](./US-42-visualizacion-vacantes-publicadas-junta-ficha-mapa.md) *(5 SP - P1)*
43. [US-43 — Registro y baja de muestras de interés sobre escuelas y vacantes desde el mapa](./US-43-registro-baja-muestras-interes-mapa.md) *(3 SP - P2)*

### Épica E09: Alertas y Cobertura de Vacantes Activas
44. [US-44 — Bandeja de notificaciones y alertas de nuevas vacantes compatibles con el perfil docente](./US-44-bandeja-notificaciones-alertas-vacantes.md) *(5 SP - P2)*
45. [US-45 — Filtrado de alertas de vacantes por escuelas de mi Formulario C, intereses y departamento](./US-45-filtrado-alertas-vacantes-formulario-c-intereses.md) *(3 SP - P2)*
46. [US-46 — Consulta del detalle analítico de una solicitud de cobertura de vacante](./US-46-consulta-detalle-analitico-vacante.md) *(2 SP - P2)*
47. [US-47 — Postulación voluntaria a solicitud de cobertura de vacante activa](./US-47-postulacion-voluntaria-cobertura-vacante.md) *(5 SP - P2)*
48. [US-48 — Emisión de comprobante digital inmutable de postulación a vacante](./US-48-emision-comprobante-pdf-postulacion-vacante.md) *(2 SP - P2)*

### Épica E10: Simulador de Carrera Docente y Recomendador SIPEC
49. [US-49 — Simulador interactivo de puntaje LOM en entorno aislado (Sandbox Dto. 257/19)](./US-49-simulador-interactivo-puntaje-lom-sandbox.md) *(5 SP - P2)*
50. [US-50 — Proyección de escuelas alcanzables al incrementar el puntaje en el Simulador de Carrera](./US-50-proyeccion-escuelas-alcanzables-simulador-carrera.md) *(5 SP - P2)*
51. [US-51 — Guardado y comparación de hasta 3 escenarios de simulación por usuario](./US-51-guardado-comparacion-3-escenarios-simulacion.md) *(3 SP - P3)*
52. [US-52 — Recomendador de capacitaciones del catálogo tipo SIPEC ponderado según Decreto 257/19](./US-52-recomendador-capacitaciones-catalogo-sipec.md) *(5 SP - P2)*
53. [US-53 — Identificación visual de rubros con mayor margen de mejora en el perfil de puntaje](./US-53-identificacion-rubros-margen-mejora-puntaje.md) *(2 SP - P2)*

### Épica E11: Asistente Virtual Docente con IA (Chatbot + LLM)
54. [US-54 — Interfaz de conversación y sugerencias rápidas del Asistente Virtual Docente](./US-54-interfaz-conversacion-sugerencias-asistente-virtual.md) *(5 SP - P2)*
55. [US-55 — Respuestas contextualizadas con los datos reales del docente en el Asistente Virtual](./US-55-respuestas-contextualizadas-datos-reales-asistente.md) *(5 SP - P2)*
56. [US-56 — Interpretación de lenguaje natural mediante modelo de lenguaje (LLM Groq) en el Asistente](./US-56-interpretacion-lenguaje-natural-llm-groq.md) *(5 SP - P2)*
57. [US-57 — Mecanismo de contingencia determinístico local ante falta de clave o caída del servicio LLM](./US-57-contingencia-deterministica-local-asistente.md) *(3 SP - P2)*
58. [US-58 — Resguardo de datos personales identificatorios (PII) en consultas al modelo de lenguaje externo](./US-58-resguardo-datos-personales-pii-consultas-llm.md) *(2 SP - P1)*

### Épica E12: Gestión y Fiscalización para Vocales de Junta
59. [US-59 — Bandeja de establecimientos asignados al Vocal con aislamiento estricto de jurisdicción](./US-59-bandeja-establecimientos-vocal-aislamiento-jurisdiccion.md) *(5 SP - P1)*
60. [US-60 — Listado, búsqueda y filtrado de aspirantes por escuela y estado de revisión](./US-60-listado-busqueda-filtrado-aspirantes-por-escuela.md) *(5 SP - P1)*
61. [US-61 — Inspección integral (Lupa) del legajo e inscripción del aspirante por parte del Vocal](./US-61-inspeccion-integral-lupa-legajo-inscripcion-vocal.md) *(5 SP - P1)*
62. [US-62 — Aprobación (Aceptación) de antecedentes del legajo por el Vocal de Junta](./US-62-aprobacion-aceptacion-antecedentes-vocal.md) *(3 SP - P1)*
63. [US-63 — Rechazo fundado de antecedentes con motivo obligatorio por el Vocal de Junta](./US-63-rechazo-fundado-antecedentes-motivo-obligatorio-vocal.md) *(5 SP - P1)*
64. [US-64 — Sincronización y escritura espejo en las tablas de la Junta en entornos de desarrollo y prueba](./US-64-sincronizacion-escritura-espejo-tablas-junta.md) *(5 SP - P2)*

### Épica E13: Gestión de Reclamos, Historial LOM y Analítica
65. [US-65 — Presentación digital de reclamos de revisión de LOM o antecedentes por el aspirante](./US-65-presentacion-digital-reclamos-revision-lom.md) *(5 SP - P2)*
66. [US-66 — Bandeja de resolución formal de reclamos para Vocales de la Junta](./US-66-bandeja-resolucion-formal-reclamos-vocal.md) *(5 SP - P2)*
67. [US-67 — Consulta de historial de inscripciones, postulaciones y resultados de LOM de ciclos anteriores](./US-67-consulta-historial-inscripciones-lom-ciclos-anteriores.md) *(3 SP - P3)*
68. [US-68 — Visualización de métricas y percentiles anonimizados de competitividad regional](./US-68-visualizacion-metricas-percentiles-competitividad-regional.md) *(5 SP - P3)*
69. [US-69 — Exportación de reportes operativos de gestión para Vocales de la Junta](./US-69-exportacion-reportes-operativos-gestion-vocales.md) *(3 SP - P3)*

### Épica E14: Gobernanza, Seguridad y Auditoría Inmutable
70. [US-70 — Registro inmutable de eventos de acceso y sesión conforme a estándares institucionales](./US-70-registro-inmutable-eventos-acceso-sesion.md) *(3 SP - P1)*
71. [US-71 — Bitácora inmutable de operaciones sobre el legajo digital y el Formulario C](./US-71-bitacora-inmutable-operaciones-legajo-formulario-c.md) *(5 SP - P1)*
72. [US-72 — Registro auditable de dictámenes de validación y rechazo emitidos por Vocales](./US-72-registro-auditable-dictamenes-validacion-rechazo-vocales.md) *(3 SP - P1)*
73. [US-73 — Consulta y filtrado del historial de auditoría de accesos y cambios](./US-73-consulta-filtrado-historial-auditoria-accesos-cambios.md) *(5 SP - P2)*
74. [US-74 — Control de acceso basado en roles (RBAC) y protección de cabeceras y secretos](./US-74-control-acceso-rbac-proteccion-secretos.md) *(5 SP - P1)*

### Sin Épica: Transversales, Operación e Interoperabilidad
75. [US-75 — Conmutación transparente de modo de integración (Oracle Lab Productivo vs. Modo Mock) y rótulos de transparencia](./US-75-conmutacion-modo-integracion-oracle-mock-rotulos.md) *(5 SP - P1)*
76. [US-76 — Parametrización administrativa de ventana de convocatoria anual y ciclo lectivo](./US-76-parametrizacion-ventana-convocatoria-ciclo-lectivo.md) *(3 SP - P2)*
77. [US-77 — Monitoreo de salud operativa y diagnóstico de conectividad a bases de datos (`/api/health`)](./US-77-monitoreo-salud-operativa-conectividad-health.md) *(2 SP - P1)*
78. [US-78 — Resiliencia y degradación elegante ante intermitencias de servicios externos o VPN](./US-78-resiliencia-degradacion-elegante-servicios-externos.md) *(5 SP - P1)*
79. [US-79 — Diseño adaptable (Responsive) y criterios de accesibilidad web para docentes y vocales](./US-79-diseno-adaptable-responsive-accesibilidad-web.md) *(5 SP - P2)*
80. [US-80 — Despliegue reproducible en contenedores Docker e inicialización automática de esquemas y datos semilla](./US-80-despliegue-contenedores-docker-inicializacion-bd.md) *(5 SP - P1)*

# Datos reales y simulados del portal

Qué muestra el portal con `INTEGRATION_MODE=prod` (el default, ADR-026), de dónde sale cada dato y cómo se lo rotula en pantalla. En `mock` todo sale de los fixtures de `src/fixtures/`.

## Datos del lab Oracle (solo lectura)

El portal solo consulta el lab: no crea tablas ni escribe en él.

| Dato | Origen | Referencia |
|------|--------|------------|
| Padrón de escuelas y catálogo del Formulario C | `VT_EMPRESAS_HABILITADA_INSCRIPCION`, `VT_MATCAR_EMPRESA`, CUE de `VM_PSX_EMPRESAS` | ADR-015 |
| Alcance de títulos (validador) | `VT_MAT_GRADO_X_TITULO`; con `ALCANCE_SQL_PATH`, el dump con docente / habilitante / supletorio | ADR-009 |
| Vacantes del mapa y promedio LOM histórico | `T_PUBLICACIONES`, `GEDUC.VT_EMP_EMPRESAS_DOM`, `T_DETALLES_INSC_JD` | ADR-016, ADR-019 |
| Puntaje LOM del docente (total) | `VT_PSX_PUNTAJES_X_EMPLEADO` | ADR-010 |
| Alertas de vacantes | `VT_PUBLI_EMPRE_MAT_ASPIRANTES` | ADR-020 |
| Antigüedad docente, cargos vigentes y trayectoria del perfil | `PEOPLENET.M4CBA_VW_CONC_DIR`, `PEOPLENET.M4CBA_VW_CONSE_CAR` (consulta por DNI en cada perfil) | ADR-030 |
| Antecedentes provinciales del legajo (títulos y cursos ya validados por la Junta) | `VT_EMPLEADOS` (CUIL exacto), `T_TITULOS_EMPLEADO` + `T_TITULOS`, `T_ITEMS_VARIO_JD` (consulta al importar) | ADR-017 |

En la versión desplegada (`INTEGRATION_MODE=foto`) estos datos salen de una copia del lab tomada con la VPN, sin el puntaje LOM por docente; la fecha de la copia figura en `/api/health` (`fotoLab.generadaEn`).

## Datos que carga el portal (PostgreSQL)

Legajo con sus PDF, Formulario C, revisión del vocal (aceptado, rechazado y observaciones), intereses del mapa, notificaciones leídas y preferencias de aviso. Se guardan en la base PostgreSQL de `DATABASE_URL` (ADR-028) y no se pierden al reiniciar la API.

## Datos calculados por el portal

| Dato | Cómo se calcula | Rótulo en pantalla |
|------|-----------------|--------------------|
| Puntaje de inscripción | Total LOM del lab si el docente figura; si no, estimación según el Dto. 257/19 sobre el legajo y la inscripción | «Puntaje estimado» con la aclaración «Dto. 257/19» |
| «Tu puntaje» en el mapa y el perfil | El mismo puntaje de referencia: LOM del lab o estimado | «Tu puntaje estimado» cuando es estimado |
| Simulador de carrera | Proyección con el Dto. 257/19 sobre escuelas y vacantes del lab | Estimación, no orden oficial (ADR-024) |
| Habilitación por título | Alcance del lab contra la materia elegida | «Sin título habilitante» cuando no habilita |

## Datos de ejemplo

Lo que todavía no tiene fuente real se rotula **«datos de ejemplo»** en pantalla.

| Dato | Por qué es de ejemplo | Rótulo en pantalla | Referencia |
|------|----------------------|--------------------|------------|
| Login CiDi | Sin credenciales sandbox de CiDi | «Usuarios de ejemplo (CiDi simulado)» en el login | ADR-005 |
| Escuelas de cada vocal | No hay acta vocal–escuela en el lab: las escuelas con inscripciones se reparten entre los vocales de forma fija | Aviso «Asignación de escuelas de ejemplo» en la junta | ADR-018 |
| Catálogo de capacitaciones recomendadas | Catálogo tipo SIPEC de ejemplo | «Recomendaciones de ejemplo» | ADR-012 |
| Aspirantes precargados | Dos aspirantes de prueba (`fixtures/demo-store.prod.seed.json`) inscriptos en escuelas reales del padrón, para que cada vocal tenga algo que revisar | Usuarios de ejemplo del login | — |

Ningún dato de ejemplo corresponde a docentes reales.

## Docente real de prueba

Para recorrer el portal con datos reales, la API puede sumar al login un docente que figura en el lab (`DOCENTE_PRUEBA_CUIL` y `DOCENTE_PRUEBA_PASSWORD` en `.env`, autorizado por la cátedra). Su puntaje LOM, antigüedad y trayectoria salen del lab. El CUIL no se versiona ni llega al navegador antes de iniciar sesión: el botón del login lo muestra oculto y pide la contraseña.

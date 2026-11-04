# Pendientes de relevamiento — Product Owner / Ministerio

Checklist para cerrar el paso de **mocks locales** a **integraciones reales**. El equipo técnico puede enchufar cada ítem cambiando el adapter en `src/server/src/integrations/` sin reescribir la UI.

**Contacto sugerido:** JTP (lab Oracle), Dirección de Sistemas, Junta de Clasificación Secundaria.

---

## Preguntas al ministerio / junta

### Autenticación y usuarios

| # | Pregunta | Para qué lo usamos |
|---|----------|-------------------|
| A1 | Credenciales sandbox CiDi OAuth2 (client id, secret, redirect URI) | Reemplazar `MockAuthAdapter` |
| A2 | Claims del token CiDi: ¿cómo derivamos rol aspirante vs vocal? | Guards y menú |

### Datos maestros

| # | Pregunta | Para qué |
|---|----------|----------|
| B1 | API o vista del **padrón de escuelas** (código `EE…`, CUE, nombre, localidad) | `PadronEscuelasRepository` — Form C búsqueda |
| B2 | Catálogo de **asignaturas/cargos por escuela** | `catalogo-asignaturas` |
| B3 | **GeoJSON** provincial oficial para mapa | `MapaRepository` |
| B4 | API de **vacantes activas** por establecimiento/asignatura | Mapa + futuro US-10 |

### Legajo y valoración

| # | Pregunta | Para qué |
|---|----------|----------|
| C1 | Endpoint/vista **SGE** antecedentes provinciales por CUIL | `SgeClient` — sync legajo |
| C2 | Lista oficial **categorías antecedentes** (acta junta TBD-04) | `antecedentes.mock.json` |
| C3 | Vista Oracle **alcance título** (habilitante / supletorio) | `AlcanceTituloRepository` — US-07 |
| C4 | Contrato **motor LOM**: ¿batch o on-demand? ¿campos desglose? | `LomValoracionPort` — US-05 (ver ADR-010) |
| C5 | ¿Existe integración **concepto profesional / EDI**? ¿URL? | Impacta puntaje; hoy fuera del portal |

### Junta y operación

| # | Pregunta | Para qué |
|---|----------|----------|
| D1 | **Acta vocal–escuela**: tabla o export CUIL vocal × escuelaId | `VocalEscuelaRepository` — US-06 |
| D2 | Regla **ventana inscripción anual** (fechas, excepciones) | TBD-07 — hoy flag `INSCRIPCION_ABIERTA` |
| D3 | Flujo **designación** vacantes en escuela (papel vs digital) | Fuera MVP; documentar alcance |

### Módulo 3 (vista preliminar en portal)

| # | Pregunta | Para qué |
|---|----------|----------|
| E1 | Baremo oficial LOM / puntos por capacitación al escalafón | **Resuelto en parte:** el estimado aplica el Dto. 257/19 (ADR-027, `baremo-decreto-257.json`). A confirmar con la junta: cursos de más de 150 h, caducidad (no aplicada) y los datos que el legajo no registra y se deducen |
| E2 | Catálogo **capacitaciones** recomendadas por brecha / demanda ministerial | `capacitaciones-sugeridas.mock.json` |

### Infraestructura

| # | Pregunta | Para qué |
|---|----------|----------|
| F1 | Acceso **lab Oracle** (host, schema, credenciales) | **Resuelto:** VPN y usuario de solo lectura operativos (ADR-026) |
| F2 | Servidor en el entorno oficial: ¿VM o Docker?, ¿quién despliega y actualiza?, ¿acceso por VPN, red interna o dominio?, ¿certificado HTTPS? | Deploy con las imágenes Docker existentes (`compose.yaml`) |
| F3 | Escritura en el lab: ¿qué esquema y usuario?, ¿se pueden crear tablas propias con prefijo **`PD24_`**?, ¿MT4D o MT4T? | Persistencia en Oracle (`PERSISTENCIA=oracle`, ADR-029) en lugar de PostgreSQL |
| F4 | ¿Se puede usar una base administrada (Neon o Supabase) para la demo con datos de prueba? | Versión desplegada para que los docentes prueben sin VPN; nunca con datos reales de docentes |
| F5 | ¿Se puede copiar a esa base una foto del lab **sin datos personales** (padrón, catálogo, alcance de títulos, vacantes publicadas y promedios LOM por escuela)? | Modo `INTEGRATION_MODE=foto`: la versión desplegada muestra datos del lab sin VPN |

---

## Tabla dato → adapter → archivo (Sprint D6)

| Dato del PO | Puerto / adapter | Archivo principal |
|-------------|------------------|-------------------|
| Usuarios CiDi | `AuthProvider` | `integrations/mock/mock-adapters.ts` → prod |
| Padrón escuelas | `PadronEscuelasRepository` | `fixtures/escuelas.mock.json` — **lab:** ADR-015 |
| Catálogo asignaturas | (mismo padron o vista aparte) | `fixtures/catalogo-asignaturas.mock.json` — **lab:** ADR-015 |
| Antecedentes SGE | `SgeClient` | `fixtures/antecedentes-provincia.mock.json` |
| Categorías legajo | `AntecedentesCatalog` | `fixtures/antecedentes.mock.json` |
| Alcance título | `AlcanceTituloRepository` | `fixtures/alcance-titulo.mock.json` — **lab:** `VT_MAT_GRADO_X_TITULO` (ADR-009) |
| Motor LOM | `LomValoracionPort` | `fixtures/puntaje.mock.json` — ADR-010 |
| Acta vocal–escuela | `VocalEscuelaRepository` | `fixtures/vocal-escuelas.mock.json` |
| GeoJSON + vacantes | `MapaRepository` | `fixtures/mapa-establecimientos.mock.json` — **lab:** ADR-016 |
| Simulador LOM | `AscensoSimuladorPort` | `fixtures/simulador-lom.mock.json` |
| Cursos sugeridos | `CapacitacionRecomendadorPort` | `fixtures/capacitaciones-sugeridas.mock.json` |
| Ventana inscripción | config + middleware Form C | `config/env.ts` — TBD-07 |
| Legajo / Form C persistido | `PersistenciaUsuarios` → PostgreSQL (ADR-028); tablas `PD24_` si el lab lo permite (F3) | `store/persistencia/` |

---

## Formato esperado de respuesta (para el equipo)

Por cada ítem, idealmente:

1. URL o nombre de vista Oracle / endpoint REST  
2. Ejemplo JSON request/response (o captura de columnas)  
3. Ambiente sandbox con datos de prueba  
4. Contacto técnico para dudas  

---

*Última actualización: octubre 2026 — Portal Docente G24.*

# Portal Docente Provincial de Córdoba — Gestión de configuración (G24)

Política de gestión de configuración del **Proyecto Final ISI — Grupo G24 (2026)**. Aplica al repositorio del producto en GitHub.

**Repositorio:** https://github.com/fabrivalseg/PF-24---Gestion-Docente-Secundaria

**Docentes:** Ing. Sergio Quinteros (titular) · Ing. Gabriel Martínez (JTP) — UTN FRC

**Versión:** 2.5 — 21-10-2026

---

## Índice

1. [Integrantes y roles](#integrantes-y-roles)
2. [Alcance de este documento](#alcance-de-este-documento)
3. [Estructura del repositorio](#estructura-del-repositorio)
4. [Reglas de nombrado](#reglas-de-nombrado)
5. [Ítems de configuración](#ítems-de-configuración)
6. [Ciclo de vida](#ciclo-de-vida)
7. [Herramientas](#herramientas)
8. [Política de ramas y pull requests](#política-de-ramas-y-pull-requests)
9. [CI/CD](#cicd)
10. [Criterio de línea base y tags](#criterio-de-línea-base-y-tags)
11. [Commits](#commits)
12. [Decisiones de arquitectura (ADR)](#decisiones-de-arquitectura-adr)
13. [Mantenimiento del SCM](#mantenimiento-del-scm)

---

## Integrantes y roles

| Legajo | Integrante |
|--------|------------|
| 95754 | Demaria, Alexis |
| 91227 | Formoso, Matías Agustín |
| 94373 | Serafini, Augusto |
| 94196 | Steffolani, Nicolás |
| 96061 | Valverde, Fabrizio |

| Rol | Responsable |
|-----|-------------|
| Product Owner | N. Steffolani |
| Scrum Master | Rotativo por sprint |
| Developers | N. Steffolani, F. Valverde, A. Serafini, A. Demaria, M. A. Formoso |

---

## Alcance de este documento

Este archivo define la **gestión de configuración del software** versionado en el repositorio del producto: estructura, nombrado, ciclo de vida de artefactos, integración continua y criterios de línea base en Git.

---

## Estructura del repositorio

```
PF-24---Gestion-Docente-Secundaria/
├── README.md
├── SCM.md
├── .gitignore
├── .gitattributes
├── src/
│   ├── README.md               guía de desarrollo del monorepo
│   ├── package.json            workspaces npm (server + web)
│   ├── .env.example            variables de entorno (copiar a .env)
│   ├── compose.yaml            Docker producción (Nginx + API + base PostgreSQL)
│   ├── compose.dev.yaml        Docker desarrollo
│   ├── compose.mock.yaml       alternativa con datos simulados
│   ├── fixtures/               datos JSON de prueba y semillas
│   ├── server/                 API REST Node.js + TypeScript
│   │   ├── db/migraciones/     esquema SQL versionado de la base del portal
│   │   └── src/
│   │       ├── modules/        módulos por dominio (auth, legajo, junta, …)
│   │       ├── integrations/   puertos y adaptadores (mock y lab Oracle)
│   │       └── store/          datos cargados por el usuario y su persistencia
│   └── web/                    frontend Angular SSR
│       └── src/app/            features y core
├── scripts/                    generadores de fixtures y diagramas
├── docs/
│   ├── README.md
│   ├── adr/                    decisiones de arquitectura
│   ├── api/                    contrato OpenAPI
│   └── diagramas/
└── .github/
    └── workflows/
        └── ci.yml
```

No commitear credenciales, archivos `.env` con secretos ni datos personales reales. Tampoco volcados de la base del portal: los datos cargados viven en la base PostgreSQL de cada entorno, no en el repositorio.

---

## Reglas de nombrado

**Archivos de documentación del equipo** (salvo convenciones del stack):

- `PF_<Tipo>_<Nombre>.<ext>`

**Excepciones en raíz:** `README.md`, `SCM.md`.

**ADRs:** `ADR-<NNN>-<titulo-corto>.md` (tres dígitos, palabras en minúsculas separadas por guiones).

**Código:** seguir convenciones del lenguaje y del framework adoptado (ver ADRs y pautas de codificación en Instancia 2).

---

## Ítems de configuración

| Ítem | Regla / nombre | Ubicación |
|------|----------------|-----------|
| Política SCM | `SCM.md` | `/` |
| README del producto | `README.md` | `/` |
| Monorepo (scripts) | `package.json` | `src/` |
| Variables de entorno (ejemplo) | `.env.example` | `src/` |
| Datos mock de desarrollo | `*.mock.json`, `README.md` | `src/fixtures/` |
| Semillas de datos de prueba | `demo-store*.seed.json` | `src/fixtures/` |
| API backend | módulos en `server/src/modules/` | `src/server/` |
| Migraciones de la base del portal | `NNN_<descripcion>.sql` (tres dígitos, en orden de aplicación) | `src/server/db/migraciones/` |
| Contenedores | `Dockerfile`, `compose*.yaml` | `src/`, `src/server/`, `src/web/` |
| Frontend Angular SSR | features en `web/src/app/` | `src/web/` |
| ADR | `ADR-NNN-*.md` | `docs/adr/` |
| Documentación técnica | `PF_Docs_*` o nombre descriptivo | `docs/` |
| Workflows CI | `*.yml` | `.github/workflows/` |
| Secretos (JWT, bases de datos, Ciudadano Digital) | `.env` del servidor de cada entorno / GitHub Secrets | no versionar |

---

## Ciclo de vida

| Artefacto | Crea | Actualiza | Cuándo |
|-----------|------|-----------|--------|
| Código y tests | Developer asignado | Autor y revisor (PR) | Durante el sprint; merge a `main` al cumplir Definition of Done |
| `SCM.md` | Sprint 0 | Cualquier integrante vía PR | Cambio de estructura, herramientas o política de ramas |
| ADR | Autor de la decisión | Equipo en PR | Antes o durante la implementación de la decisión |
| `README.md` | Equipo | PR | Cambios de setup, enlaces o descripción del producto |
| Workflows CI | Equipo | PR | Cambio de pipeline o herramientas de build/test |
| Migración SQL | Developer del cambio de esquema | No se edita una vez en `main`: los cambios van en una migración nueva | Cada cambio en las tablas de la base del portal |
| Tags de línea base | Acuerdo del equipo | — | Devolución docente o hito de sprint |

---

## Herramientas

| Ámbito | Herramienta |
|--------|-------------|
| Control de versiones | Git + GitHub |
| Revisión de código | Pull Requests |
| Integración continua | GitHub Actions |
| Contenedores | Docker + Docker Compose |
| Base de datos del portal | PostgreSQL (ADR-028), con auditoría de accesos y cambios (ADR-032) |
| Base de la Junta | Oracle: lectura; escritura solo en desarrollo y prueba, sobre una lista cerrada de tablas (ADR-029) |
| Autenticación | Ciudadano Digital, nivel 2 o superior (ADR-031) |
| Backlog | GitHub Issues (u otra herramienta acordada en planning) |

---

## Política de ramas y pull requests

- **`main`:** rama estable; integración solo mediante Pull Request.
- **`feature/<descripcion>`** — nueva funcionalidad.
- **`fix/<descripcion>`** — corrección.
- **`docs/<descripcion>`** — documentación (`SCM.md`, README, ADRs).

Cada PR requiere al menos **un revisor** distinto del autor, comentarios resueltos antes del merge y CI en verde cuando el pipeline ejecute build o tests.

---

## CI/CD

GitHub Actions ejecuta build y pruebas automáticas de `src/server` (API) y build de `src/web` (Angular SSR), además de verificar la documentación base del repositorio.

---

## Criterio de línea base y tags

Se registra una línea base cuando:

1. Existe devolución docente sobre una instancia, o
2. El equipo acuerda un hito de sprint (demo, cierre de iteración, etc.).

Registrar con commit en `main` y **tag anotado**. Comunicar al equipo.

| Tag | Significado |
|-----|-------------|
| `INST<n>-aprobado` | Instancia *n* con devolución favorable |
| `INST<n>-no_aprobado` | Instancia *n* con devolución desfavorable |
| `S<n>-aprobado` | Hito del sprint *n* |
| `P<n>` | Parcial de cursada |

Ejemplo:

```bash
git tag -a S1-aprobado -m "Sprint 1 — linea base acordada"
git push origin S1-aprobado
```

---

## Commits

Formato: `<accion>: <descripcion breve>`

| Acción | Uso |
|--------|-----|
| `add:` | Nuevo archivo o funcionalidad |
| `update:` | Mejora de existente |
| `fix:` | Corrección |
| `remove:` | Eliminación |
| `refactor:` | Reorganización sin cambio de comportamiento |
| `docs:` | Documentación (`SCM.md`, README, ADRs) |

Referenciar el identificador de historia en el mensaje cuando exista tablero de trabajo.

---

## Decisiones de arquitectura (ADR)

Las decisiones técnicas relevantes se registran en `docs/adr/` siguiendo el formato ADR. La tabla maestra y el detalle de cada decisión figuran en **Instancia 2** y en los archivos individuales del directorio; el índice vigente está en `docs/adr/README.md`.

Una decisión aceptada no se reescribe: si cambia, se registra un ADR nuevo que la reemplaza y se indica en ambos.

---

## Mantenimiento del SCM

Actualizar este documento **vía Pull Request** cuando cambie la estructura del repositorio, las herramientas de integración, la política de ramas o el criterio de línea base. Revisión por al menos un integrante distinto del autor.

---

*Versión 2.5 — 21-10-2026 — Grupo G24*

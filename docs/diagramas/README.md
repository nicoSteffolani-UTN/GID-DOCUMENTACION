# Diagramas MVP — Portal Docente G24


> **Índice:** [`diagramas/README.md`](./diagramas/README.md)

Documentación visual del MVP: cadena P1 (login → legajo → Form C → validador) y **P2** (puntaje, junta vocal, mapa Leaflet).

**Herramienta:** [Mermaid](https://mermaid.js.org/) — se renderiza en GitHub y VS Code (Markdown Preview).

**Referencias:** `docs/adr/` · `SCM.md` · Instancia 2 (Sprint 0).

**Leyenda**

| Símbolo | Significado |
|---------|-------------|
| Sólido | Implementado en repo |
| Punteado | Futuro / TBD / fuera MVP UI |
| AS-IS | Sistema legacy (referencia) |
| TO-BE | Portal nuevo (propuesto) |

---

## Índice

| # | Diagrama | Tipo |
|---|----------|------|
| 1 | [Contexto del sistema (C4 — nivel 1)](./01-contexto-del-sistema-c4-nivel-1.md) | Arquitectura |
| 2 | [Arquitectura de contenedores (C4 — nivel 2)](./02-arquitectura-de-contenedores-c4-nivel-2.md) | Arquitectura |
| 3 | [Despliegue local vs producción](./03-despliegue-local-vs-produccion.md) | Arquitectura |
| 4 | [Componentes backend (capas por módulo)](./04-componentes-backend-capas-por-modulo.md) | Arquitectura |
| 5 | [Frontend — rutas y features](./05-frontend-rutas-y-features.md) | Arquitectura |
| 6 | [Cadena de negocio TO-BE (MVP codeado)](./06-cadena-de-negocio-to-be-mvp-codeado.md) | Arquitectura |
| 7 | [Diagrama de clases — dominio (conceptual)](./07-diagrama-de-clases-dominio-conceptual.md) | Clases |
| 8 | [Diagrama de clases — backend (implementación)](./08-diagrama-de-clases-backend-implementacion.md) | Clases |
| 9 | [Diagrama de clases — frontend](./09-diagrama-de-clases-frontend.md) | Clases |
| 10 | [Estados — sesión del aspirante](./10-estados-sesion-del-aspirante.md) | Estado |
| 11 | [Estados — carga de antecedente (dominio)](./11-estados-carga-de-antecedente-dominio.md) | Estado |
| 12 | [Estados — Formulario C (UI + persistencia)](./12-estados-formulario-c-ui-persistencia.md) | Estado |
| 13 | [Estados — inscripción anual (config)](./13-estados-inscripcion-anual-config.md) | Estado |
| 14 | [Secuencia — login mock CiDi](./14-secuencia-login-mock-cidi-us-01.md) | Secuencia |
| 15 | [Secuencia — registrar antecedente](./15-secuencia-registrar-antecedente-us-03.md) | Secuencia |
| 16 | [Secuencia — postular materia con validador](./16-secuencia-postular-materia-con-validador-us-04-us-.md) | Secuencia |
| 17 | [Secuencia — guardar Formulario C (topes + validación final)](./17-secuencia-guardar-formulario-c-topes-validacion-fi.md) | Secuencia |
| 18 | [Mapa API REST (implementado)](./18-mapa-api-rest-implementado.md) | Arquitectura |
| 19 | [Integraciones — real vs mock (evolución)](./19-integraciones-real-vs-mock-evolucion.md) | Arquitectura |
| 20 | [Secuencia — revisión vocal US-06](./20-secuencia-us06-revision-vocal.md) | Secuencia |
| 21 | [Secuencia — puntaje US-05](./21-secuencia-us05-puntaje.md) | Secuencia |
| 22 | [Secuencia — mapa Leaflet PD-21](./22-secuencia-pd21-mapa-leaflet.md) | Secuencia |

## Documento consolidado

Todos los diagramas en un solo archivo: [DIAGRAMAS_MVP.md](../DIAGRAMAS_MVP.md)

Exportar PNG/SVG: [mermaid.live](https://mermaid.live).

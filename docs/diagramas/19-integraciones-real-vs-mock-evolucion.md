# Integraciones — real vs mock (evolución)

Actualizado agosto 2026 — lab MT4D cableado parcialmente.

```mermaid
flowchart TB
  subgraph mock [INTEGRATION_MODE mock / sql]
    M1[usuarios.mock.json]
    M2[antecedentes.mock.json]
    M3[escuelas.mock.json]
    M4[catalogo-asignaturas.mock.json]
    M5[alcance-titulo.mock.json]
    M6[vocal-escuelas.mock.json]
    M7[puntaje.mock.json]
    M8[mapa-establecimientos.mock.json]
    M9[demo-store.seed.json]
    MEM[user-data.store JSON]
  end

  subgraph lab [INTEGRATION_MODE prod + VPN]
    L1[VT_MAT_GRADO_X_TITULO]
    L2[VT_EMPRESAS + VT_MATCAR]
    L3[T_PUBLICACIONES + VT_EMP_EMPRESAS_DOM + VT_MATERIAS_JUNTA + VT_MATCAR_EMPRESA (incl. LOM secundaria)]
  end

  subgraph pendiente [Pendiente ministerio / JTP]
    P1[CiDi OAuth2]
    P2[Motor LOM desglosado]
    P3[Acta vocal escuela]
    P4[SGE antecedentes CUIL]
    P5[Persistencia Oracle]
  end

  mock -.->|opt-in prod| lab
  mock -.->|futuro| pendiente
```

---

[← Índice diagramas](./README.md) · [Demo Oracle lab](../DEMO_ORACLE_LAB.md)

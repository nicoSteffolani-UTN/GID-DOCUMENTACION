# Arquitectura de contenedores (C4 — nivel 2)

Monolito modular (ADR-004). Dev local: fixtures JSON + store en memoria; prod: Oracle (ADR-003).

```mermaid
flowchart LR
  subgraph cliente [Cliente]
    WEB[Angular SSR / ng serve :4200]
  end

  subgraph api [API Node Express :3000]
    AUTH[modules/auth]
    LEG[modules/legajo]
    FC[modules/formulario-c]
    VAL[modules/validador]
    STORE[(user-data.store memoria)]
  end

  subgraph datos_dev [Datos dev]
    FIX[src/fixtures/*.mock.json]
  end

  subgraph datos_prod [Datos prod — futuro]
    ORA[(Oracle)]
    VISTA[Vista alcance título]
  end

  WEB -->|REST /api/* JWT| AUTH
  WEB --> LEG
  WEB --> FC
  WEB --> VAL

  AUTH --> FIX
  LEG --> FIX
  LEG --> STORE
  FC --> FIX
  FC --> STORE
  FC --> VAL
  VAL --> FIX

  LEG -.-> ORA
  FC -.-> ORA
  VAL -.-> VISTA
```

---

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

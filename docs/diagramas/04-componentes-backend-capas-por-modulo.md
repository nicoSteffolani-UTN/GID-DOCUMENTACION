# Componentes backend (capas por módulo)

Patrón repetido en cada US: **routes → controller → service → fixtures/store**.

```mermaid
flowchart TB
  subgraph express [Express app.ts]
    MW[CORS + JSON + jwtMiddleware]
    RL[requireRole aspirante / vocal]
  end

  subgraph auth [auth]
    AR[auth.routes]
    AC[auth.controller]
    AS[auth.service]
  end

  subgraph legajo [legajo]
    LR[legajo.routes]
    LC[legajo.controller]
    LS[legajo.service]
  end

  subgraph formc [formulario-c]
    FR[formulario-c.routes]
    FC[formulario-c.controller]
    FS[formulario-c.service]
  end

  subgraph val [validador]
    VR[validador.routes]
    VC[validador.controller]
    VS[validador.service]
  end

  subgraph junta [junta US-06]
    JR[junta.routes]
    JC[junta.controller]
    JS[junta.service]
  end

  subgraph puntaje [puntaje US-05]
    PR[puntaje.routes]
    PC[puntaje.controller]
    PS[puntaje.service]
  end

  subgraph mapa [mapa PD-21]
    MR[mapa.routes]
    MC[mapa.controller]
    MS[mapa.service]
  end

  MW --> AR & LR & FR & VR & JR & PR & MR
  RL --> LR & FR & VR & JR & PR & MR
  AR --> AC --> AS
  LR --> LC --> LS
  FR --> FC --> FS
  VR --> VC --> VS
  JR --> JC --> JS
  PR --> PC --> PS
  MR --> MC --> MS

  FS --> VS
  FS --> LS
  JS --> LS
  JS --> FS
  LS --> STORE[(user-data.store)]
  FS --> STORE
  AS & LS & FS & VS & JS & PS & MS --> FIX[(fixtures)]
```

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

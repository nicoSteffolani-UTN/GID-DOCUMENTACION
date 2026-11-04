# Frontend — rutas y features (P2 + mapa)

```mermaid
flowchart LR
  subgraph core [core/]
    AUTH_S[auth.service]
    AUTH_G[auth.guard]
    ROLE_G[aspiranteGuard / vocalGuard]
    AUTH_I[auth.interceptor]
    SHELL[app-shell nav por rol]
    LEG_S[legajo.service]
    FC_S[formulario-c.service]
    PUN_S[puntaje.service]
    MAP_S[mapa.service]
    SIM_S[simulador.service]
    CAP_S[capacitaciones.service]
    JUN_S[junta.service]
  end

  subgraph shared [shared/]
    PH[page-header]
    RD[rechazo-dialog]
  end

  subgraph aspirante [features aspirante]
    LOGIN[login]
    HOME[home 4 pasos]
    LEG[legajo + observaciones]
    FORM[formulario-c]
    PUN[puntaje]
    MAP[mapa Leaflet + interés US-10 lite]
    SIM[simulador ascenso]
    CAP[capacitaciones]
  end

  subgraph vocal [features vocal]
    JUN[junta revision]
  end

  LOGIN --> AUTH_S
  SHELL --> AUTH_S
  HOME --> LEG_S & FC_S & PUN_S
  LEG --> LEG_S
  FORM --> FC_S
  PUN --> PUN_S
  MAP --> MAP_S
  SIM --> SIM_S
  CAP --> CAP_S
  JUN --> JUN_S
  JUN --> RD
  FC_S -->|validar| VAL_API[/api/validador/habilitacion/]

  AUTH_G --> SHELL
  ROLE_G --> HOME & LEG & FORM & PUN & MAP & SIM & CAP & JUN
  SHELL --> HOME & LEG & FORM & PUN & MAP & SIM & CAP & JUN
  AUTH_I --> AUTH_S
```

**Rutas:** `/login` · `/home` · `/legajo` · `/formulario-c` · `/puntaje` · `/mapa` · `/simulador` · `/capacitaciones` (aspirante) · `/junta` (vocal).

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

# Estados — carga de antecedente (dominio)

Estados `PENDIENTE` / `SIN_PENDIENTE` / `RECHAZADO` persistidos en `user-data.store` (dev JSON). La revisión vocal (US-06) actualiza el estado vía API junta. TBD-05: regla técnica definitiva en Oracle prod.

```mermaid
stateDiagram-v2
  [*] --> SinAntecedentes

  SinAntecedentes --> Registrado : POST /api/legajo

  state Registrado {
    [*] --> SinRevision
    SinRevision --> Pendiente : doc nueva este año\no alcances incompletos
    SinRevision --> SinPendiente : sin doc nueva\neste año
    Pendiente --> SinPendiente : vocal valida batch\nfuera MVP UI
  }

  Registrado --> Registrado : nuevo antecedente
```

---

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

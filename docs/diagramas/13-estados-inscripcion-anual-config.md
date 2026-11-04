# Estados — inscripción anual (config)

Flag mock `INSCRIPCION_ABIERTA` (TBD-07). Regla real pendiente junta.

```mermaid
stateDiagram-v2
  [*] --> VentanaCerrada : INSCRIPCION_ABIERTA=false
  [*] --> VentanaAbierta : INSCRIPCION_ABIERTA=true

  VentanaCerrada --> VentanaAbierta : config JTP TBD-07
  VentanaAbierta --> VentanaCerrada : cierra convocatoria

  state VentanaAbierta {
    [*] --> AceptaFormC
    AceptaFormC --> AceptaFormC : PUT formulario-c
  }

  state VentanaCerrada {
    [*] --> RechazaFormC
    RechazaFormC --> RechazaFormC : PUT → 400
  }
```

---

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

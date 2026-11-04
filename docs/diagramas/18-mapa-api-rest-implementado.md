# Mapa API REST (implementado)

```mermaid
flowchart LR
  subgraph public [Público]
    H[GET /api/health]
    ML[POST /api/auth/mock-login]
  end

  subgraph jwt_asp [JWT — aspirante]
    ME[GET /api/auth/me]
    LO[POST /api/auth/logout]
    LC[GET /api/legajo/categorias]
    LG[GET /api/legajo]
    LA[POST /api/legajo]
    LD[DELETE /api/legajo/:id]
    LS[POST /api/legajo/sincronizar-provincia]
    FE[GET /api/formulario-c/escuelas?q=]
    FC[GET /api/formulario-c/catalogo?escuelaId=]
    FG[GET /api/formulario-c]
    FEST[GET /api/formulario-c/estado]
    FP[PUT /api/formulario-c]
    VH[POST /api/validador/habilitacion]
    PU[GET /api/puntaje/inscripcion]
    MA[GET /api/mapa/establecimientos]
    MAS[GET /api/mapa/asignaturas]
  end

  subgraph jwt_voc [JWT — vocal]
    JE[GET /api/junta/escuelas]
    JA[GET /api/junta/escuelas/:id/aspirantes]
    JL[GET /api/junta/aspirantes/:cuil/legajo]
    JOK[POST /api/junta/aspirantes/:cuil/aceptar]
    JNO[POST /api/junta/aspirantes/:cuil/rechazar]
  end
```

Roles: `requireRole` en rutas P2 (ADR-014). Aspirante recibe 403 en `/api/junta/*`; vocal en legajo, Form C, validador, puntaje y mapa.

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

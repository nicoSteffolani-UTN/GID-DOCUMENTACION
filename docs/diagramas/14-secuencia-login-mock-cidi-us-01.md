# Secuencia — login mock CiDi

```mermaid
sequenceDiagram
  autonumber
  actor U as Usuario
  participant W as LoginComponent
  participant AS as AuthService web
  participant API as POST /api/auth/mock-login
  participant S as AuthService server

  U->>W: CUIL + contraseña
  W->>AS: login(cuil, password)
  AS->>API: POST body
  API->>S: mockLogin()
  S->>S: valida fixture + nivel CiDi >= 2
  S-->>API: JWT + user (rol aspirante o vocal)
  API-->>AS: 200
  AS->>AS: sessionStorage token
  AS-->>W: OK
  alt rol vocal
    W->>W: navigate /junta
  else rol aspirante
    W->>W: navigate /home
  end
```

Hints en UI: aspirante `20123456789` / vocal `27999888777` (contraseña `demo1234`).

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

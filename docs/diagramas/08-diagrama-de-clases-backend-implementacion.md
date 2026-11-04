# Diagrama de clases — backend (implementación)

```mermaid
classDiagram
  direction TB

  class AuthService {
    -usuarios: MockUsuario[]
    +mockLogin(body): LoginSuccessResponse
    +getUserFromToken(token): AuthUserResponse
  }

  class LegajoService {
    -categorias: CategoriaAntecedente[]
    +listCategorias()
    +getLegajo(cuil)
    +altaAntecedente(cuil, body)
    +getTitulosCodigos(cuil): string[]
  }

  class FormularioCService {
    -escuelas: EscuelaFixture[]
    -catalogo: Record~escuelaId, CatalogoItem[]~
    -legajoService: LegajoService
    -validadorService: ValidadorService
    +buscarEscuelas(q)
    +getCatalogo(escuelaId)
    +getFormulario(cuil)
    +guardarFormulario(cuil, body)
  }

  class ValidadorService {
    -alcance: TituloAlcance[]
    +evaluarHabilitacion(titulos, postulacion)
    +evaluar(body)
  }

  class UserDataStore {
    <<singleton memoria>>
    +getLegajo(cuil)
    +setLegajo(cuil, items)
    +getFormulario(cuil)
    +setFormulario(cuil, data)
  }

  FormularioCService --> LegajoService : titulos
  FormularioCService --> ValidadorService : habilitacion
  LegajoService --> UserDataStore
  FormularioCService --> UserDataStore
  ValidadorService ..> FixturesAlcance : mock ADR-009
  LegajoService ..> FixturesAntecedentes
  FormularioCService ..> FixturesEscuelas
  FormularioCService ..> FixturesCatalogo
```

Constantes de negocio en código:

| Constante | Valor | Fuente |
|-----------|-------|--------|
| `MAX_ESCUELAS` | 3 | Acta junta |
| `MAX_ITEMS_POR_ESCUELA` | 5 | Acta junta [36:00] |
| `CIDI_NIVEL_MINIMO` | 2 | Política CiDi mock |

---

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

# Diagrama de clases — dominio (conceptual)

Entidades de negocio. Algunos atributos son **TBD** (marcados).

```mermaid
classDiagram
  class Aspirante {
    +cuil: string
    +nombre: string
    +cidiNivel: int
    +rol: aspirante|vocal
  }

  class Antecedente {
    +id: UUID
    +categoriaId: string
    +tituloCodigo: string?
    +descripcion: string?
    +archivoNombre: string?
    +creadoEn: datetime
    +estadoRevision: PENDIENTE|SIN_PENDIENTE
  }

  class CategoriaAntecedente {
    +id: string
    +nombre: string
    +requiereAdjunto: bool
  }

  class FormularioC {
    +postulaciones: PostulacionEscuela[]
  }

  class PostulacionEscuela {
    +escuelaId: string
    +escuelaNombre: string
    +items: PostulacionItem[]
  }

  class PostulacionItem {
    +codigo: string
    +nombre: string
    +tipo: asignatura|cargo
  }

  class Escuela {
    +id: string
    +nombre: string
    +cue: string
    +codigoEmpresa: string
  }

  class TituloAlcance {
    +codigo: string
    +nombre: string
    +habilita: string[]
  }

  Aspirante "1" --> "*" Antecedente : carga
  Antecedente --> CategoriaAntecedente
  Aspirante "1" --> "0..1" FormularioC : inscribe
  FormularioC *-- PostulacionEscuela
  PostulacionEscuela *-- PostulacionItem
  PostulacionEscuela --> Escuela
  TituloAlcance ..> PostulacionItem : habilita codigo
```

---

---

[← Índice diagramas](./README.md) · [Documento completo](../DIAGRAMAS_MVP.md)

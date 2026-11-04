# [ID: US-31] Emisión y descarga del comprobante digital PDF sellado del Formulario C

- **Épica:** E06 - Convocatoria Anual e Inscripción en Formulario C
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante que confirmó su Formulario C,  
> **quiero** descargar un comprobante digital en formato PDF con todos los datos de mi inscripción, fecha/hora oficial y código de operación,  
> **para** conservar un respaldo documental inalterable de mi presentación anual ante el Ministerio de Educación.

## Reglas de Negocio y Conexiones Externas
- **Validez Documental:** El comprobante incluye identificación del docente (Nombre, Apellido, CUIL), ciclo lectivo, marca temporal exacta del servidor, código único de comprobante y el detalle completo de los establecimientos y asignaturas confirmadas.

## Criterios de Aceptación
1. **Debe** habilitar el botón **«Descargar comprobante PDF»** inmediatamente después de confirmar el Formulario C y en cualquier ingreso posterior durante el ciclo lectivo.
2. **Debe** incluir en el documento PDF generado: encabezado institucional, datos del aspirante (Nombre completo y CUIL), ciclo lectivo, fecha y hora exacta de confirmación, código identificador de operación, y el listado detallado de las escuelas (código y nombre) con sus respectivas asignaturas/cargos inscriptos.
3. **Debe** impedir la emisión de comprobantes oficiales sobre formularios que aún se encuentren en estado `Borrador` o sin confirmar.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar descargar el comprobante PDF de un Formulario C confirmado y verificar que el archivo se abre correctamente y contiene el CUIL, la fecha/hora y las escuelas/materias exactas seleccionadas. *(Pasa)*
- **CP-02:** Probar intentar descargar el comprobante antes de confirmar el Formulario C y comprobar que la opción no está disponible para borradores. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Generación de documento PDF estructurado a partir de los datos confirmados de la inscripción. |
| **Esfuerzo** | Bajo | Plantilla de comprobante institucional y acción de descarga en el módulo de Formulario C. |
| **Incertidumbre** | Nula | Datos ya persistidos y validados en el paso de confirmación. |

# [ID: US-11] Carga manual de títulos y antecedentes externos con documento PDF probatorio

- **Épica:** E03 - Gestión del Legajo Digital y Antecedentes
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 8 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** registrar manualmente en mi legajo digital mis títulos, posgrados, cursos de capacitación u otros antecedentes externos adjuntando el certificado probatorio en formato PDF,  
> **para** que la Junta de Clasificación pueda verificarlos y computarlos en mi valoración anual sin necesidad de presentar copias en soporte papel.

## Reglas de Negocio y Conexiones Externas
- **Normativa:** Clasificación de antecedentes según el Decreto Provincial Nº 257/19 (`Titulo`, `Posgrado`, `Capacitacion`, `Antiguedad`, `Concepto`, `Otro`).
- **Restricción Técnica:** Solo se admiten archivos probatorios en formato **PDF** (`application/pdf` / `.pdf`) con un tamaño máximo estricto de **1 MB** por documento.
- **Persistencia:** Base de datos PostgreSQL del portal (`antecedente_usuario`, ADR-028) y replicación espejo en tablas de Junta cuando corresponde (ADR-029).

## Criterios de Aceptación
1. **Debe** proveer un formulario en `/legajo` (`POST /api/legajo/antecedentes`) con los campos obligatorios: Categoría (`Titulo`, `Posgrado`, `Capacitacion`, `Antiguedad`, `Concepto`, `Otro`), Título/Denominación (entre 3 y 200 caracteres), Institución emisora (entre 2 y 200 caracteres) y Fecha de emisión (`YYYY-MM-DD`).
2. **Debe** solicitar la carga horaria en horas reloj (valor numérico entero positivo) cuando la categoría corresponda a capacitación o formación continua.
3. **Debe** validar que la fecha de emisión del antecedente no sea una fecha futura respecto de la fecha actual del servidor.
4. **Debe** exigir la carga de un archivo adjunto probatorio exclusivamente en formato **PDF** (`.pdf` / `application/pdf`).
5. **Debe** rechazar en cliente y en servidor cualquier archivo cuyo peso supere el límite máximo de **1 MB (1.048.576 bytes)**.
6. **Debe** registrar todo antecedente cargado manualmente con estado inicial **`Pendiente`** (`Pendiente de Validación`) y origen `manual`.
7. **Debe** impedir la carga duplicada de un antecedente con idéntica denominación, institución y fecha de emisión para el mismo docente.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar registrar un curso de capacitación válido completando todos los campos obligatorios y adjuntando un archivo PDF de 450 KB, verificando que aparece en el legajo con estado `Pendiente`. *(Pasa)*
- **CP-02:** Probar adjuntar un archivo en formato imagen (`.jpg` / `.png`) o documento Word (`.docx`) y verificar que el sistema bloquea la carga indicando que solo se permite PDF. *(Falla controlada)*
- **CP-03:** Probar adjuntar un archivo PDF de 2,3 MB y verificar que el sistema rechaza la subida por superar el tope de 1 MB. *(Falla controlada)*
- **CP-04:** Probar ingresar una fecha de emisión futura y verificar que el validador impide el envío del formulario. *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Validación de multipart/base64, control de MIME type y tamaño de archivo en frontend y backend, persistencia relacional. |
| **Esfuerzo** | Alto | Construcción del formulario reactivo en Angular, manejo de archivos PDF y endpoint transaccional en Node.js. |
| **Incertidumbre** | Baja | Reglas de negocio y restricciones técnicas completamente especificadas. |

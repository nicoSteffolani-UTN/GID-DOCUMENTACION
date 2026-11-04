# [ID: US-14] Previsualización y descarga del certificado PDF adjunto en el legajo

- **Épica:** E03 - Gestión del Legajo Digital y Antecedentes
- **Prioridad:** P2 (Media)
- **Estimación:** 2 SP
- **Rol / Actor:** Docente Aspirante / Vocal de Junta

## Descripción (Narrativa)
> **Como** docente aspirante o vocal evaluador de la Junta,  
> **quiero** abrir o descargar directamente desde el sistema el documento PDF adjunto a cada antecedente del legajo,  
> **para** corroborar el contenido, firmas, carga horaria y validez legal del certificado digitalizado sin salir de la plataforma.

## Reglas de Negocio y Conexiones Externas
- **Seguridad de Acceso:** Un docente aspirante solo puede acceder a los archivos PDF de su propio legajo; un vocal de Junta solo puede acceder a los archivos PDF de los aspirantes bajo su ámbito de evaluación.

## Criterios de Aceptación
1. **Debe** mostrar un botón o enlace de **«Ver certificado PDF»** en cada antecedente del legajo que posea archivo probatorio adjunto.
2. **Debe** abrir el documento PDF en el visor integrado o en una pestaña segura del navegador respetando el nombre original del archivo (`pdfNombre`).
3. **Debe** validar en el backend que el usuario solicitante sea el titular del legajo o un vocal con permisos sobre el aspirante antes de entregar el contenido binario/base64 del PDF.
4. **Debe** deshabilitar o indicar «Certificado validado por padrón oficial (sin PDF adjunto)» cuando el antecedente provenga de sincronización directa de base de datos sin archivo físico.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar hacer clic en «Ver certificado PDF» sobre un antecedente cargado manualmente y verificar que el documento PDF se visualiza correctamente. *(Pasa)*
- **CP-02:** Probar como vocal en la vista de revisión de legajo abrir el PDF probatorio de un aspirante de su escuela asignada y verificar su correcta visualización. *(Pasa)*
- **CP-03:** Probar acceder al documento PDF de otro aspirante manipulando el identificador en la petición con rol `aspirante` y verificar el rechazo por permisos (HTTP 403/404). *(Falla controlada)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | Entrega segura de stream/DataURL de archivo `application/pdf` con control de pertenencia. |
| **Esfuerzo** | Bajo | Acción de visualización en la fila del antecedente y validación de autorización en API. |
| **Incertidumbre** | Nula | Formato estándar PDF soportado nativamente por los navegadores modernos. |

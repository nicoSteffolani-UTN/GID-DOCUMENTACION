# Documentación de la API

[`openapi.yaml`](./openapi.yaml) describe la API REST del portal en OpenAPI 3.0.3: las 30 operaciones de `src/server`, con parámetros, cuerpos, respuestas, errores y roles.

## Cómo verla

- **Swagger Editor:** abrir https://editor.swagger.io y pegar el contenido del archivo (o *File → Import file*).
- **VS Code:** extensión *OpenAPI (Swagger) Editor* u otra equivalente, con vista previa.
- **Probar contra la API local:** en el visor, elegir el servidor `http://localhost:3000`, ejecutar `POST /api/auth/mock-login` con un usuario demo (ver `src/fixtures/README.md`), copiar el `token` y cargarlo en *Authorize*.

## Mantenimiento

Actualizar este archivo en el mismo PR que agregue, quite o cambie una ruta, un cuerpo o un código de respuesta en `src/server/src/modules/*/`. La especificación es documentación: el servidor no la usa para validar requests.

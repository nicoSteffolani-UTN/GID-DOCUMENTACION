# ADR-021: Contenerización con Docker y Docker Compose

**Estado:** Aceptado (registro retroactivo)  
**Fecha de la decisión:** 2026-07-29 (PR #12, integrado el 2026-08-04)  
**Registrado:** 2026-09-27  
**Relacionado:** ADR-001 (Angular SSR), ADR-003 (Oracle), ADR-004 (monolito modular), ADR-005 (JWT)

> Numeración: ADR-020 está reservado por la rama `feature/portal-docente-alertas` (alertas de vacantes).

## Contexto

El equipo trabaja en Windows, Linux y macOS y necesita levantar API y frontend de forma reproducible para desarrollo, demos a la cátedra y un futuro ambiente de staging. La documentación de alcance prevé infraestructura on-premise. Hasta julio de 2026 la aplicación solo se ejecutaba con `npm run dev:server` y `npm run dev:web`.

## Decisión

Distribuir la aplicación en **dos imágenes Docker independientes** con builds multi-etapa y orquestarlas con **Docker Compose V2**, con archivos separados para desarrollo y producción (`src/compose.dev.yaml`, `src/compose.yaml`).

### Imágenes

| Imagen | Base (versión fijada) | Etapa `production` |
|--------|-----------------------|--------------------|
| Backend (`server/Dockerfile`) | `node:22.23.1-alpine3.23` | TypeScript compilado, solo dependencias productivas, sin `npm`/`npx`/`yarn`/`corepack`, sin source maps; usuario `node`; puerto interno 3000 |
| Frontend (`web/Dockerfile`) | Node para compilar + `nginx:1.28.3-alpine3.23-slim` | Solo el bundle `web/dist/web/browser` servido por Nginx como SPA (`try_files`); usuario `nginx`; puerto interno 8080 |

Ambas imágenes tienen etapa `development` para Compose de desarrollo y `HEALTHCHECK` propio (`/api/health` en backend, `/healthz` en frontend).

### Producción (`compose.yaml`)

- Se publica **solo Nginx** (`FRONTEND_PORT`, default 8080). El backend no publica puertos; Nginx redirige `/api/*` a `backend:${BACKEND_PORT}` en la red `portal_docente_internal` (`internal: true`).
- Persistencia: el store JSON (`/app/.data`) se guarda en el volumen `portal_docente_data`. No se incorpora base de datos (ver ADR-003).
- Endurecimiento: filesystem raíz de solo lectura con `tmpfs` en `/tmp`, `cap_drop: ALL`, `no-new-privileges`, límites de procesos, CPU y memoria, reinicio `unless-stopped`.
- `JWT_SECRET` es obligatorio: Compose falla si está vacío (`${JWT_SECRET:?}`) y la API rechaza valores de desarrollo en producción (`config/env.ts`). El secreto nunca se copia a la imagen.
- Cabeceras de seguridad en Nginx: `X-Content-Type-Options`, `X-Frame-Options: DENY`, `Referrer-Policy`, `Permissions-Policy`.

### Desarrollo (`compose.dev.yaml`)

- `ng serve` (puerto 4200, polling cada 1000 ms para detectar cambios en cualquier SO) y `tsx watch` (puerto 3000 publicado), con el código montado por bind mount y `node_modules` en volúmenes separados.

### Versionado de imágenes

Versiones de Node y Nginx fijadas por patch; se prohíben etiquetas flotantes (`latest`, `lts`, `stable`, `alpine`). Revisión mensual de avisos de seguridad (ver `src/README.md#docker`).

## Alternativas

No quedaron registradas en el PR ni en la documentación del equipo.

## Consecuencias

**Positivas**

- Entorno reproducible para todo el equipo y para demos, sin instalar Node localmente.
- Superficie de exposición reducida en producción: un único puerto público, API en red interna, contenedores no-root y de solo lectura.
- Imágenes de producción más chicas y sin herramientas de build.

**Negativas / limitaciones**

- **SSR no se ejecuta en producción:** el contenedor sirve el bundle de navegador como SPA. Las fuentes SSR se conservan (ADR-001), pero la ventaja de renderizado en servidor no aplica al despliegue actual.
- **El lab requiere la VPN en el host** (actualizado 2026-10-07, ver más abajo): los Compose usan el lab por defecto (ADR-026); sin VPN hay que sumar `compose.mock.yaml`.
- La persistencia sigue siendo un archivo JSON en un volumen; no apto para concurrencia ni para datos reales.
- Nginx no define todavía Content-Security-Policy: Google Fonts, Leaflet y los tiles de mapas requieren una política específica validada.
- Angular 19 está fuera de soporte; su actualización se planifica por separado.

## Actualización 2026-10-07: lab Oracle en contenedores

Para correr en Docker lo mismo que `npm run dev:server` con `INTEGRATION_MODE=prod`, que pasa a ser el default (ADR-026), `compose.yaml` y `compose.dev.yaml`:

- Pasan a la API `INTEGRATION_MODE` (default `prod`), `DB_USER`, `DB_PASSWORD`, `ORACLE_HOST`, `ORACLE_PORT`, `ORACLE_SERVICE` y `ORACLE_NOTIF_CONNECT` desde `src/.env`. Los secretos siguen fuera de la imagen.
- Montan el dump `TITULOS_ALCANCES` de solo lectura en `/data/alcance/01_alcance_titulo.sql`; `ALCANCE_SQL_PATH` del `.env` es la ruta en el host. Si no está definida, la variable llega vacía al contenedor y el alcance sale de Oracle, como en local.
- En producción, la red `portal_docente_egress` va **solo al backend**: llega al lab por la VPN del host (verificado con Docker Desktop y OpenVPN) y a Groq si hay clave. Nginx sigue únicamente en la red interna y es el único puerto publicado.
- Amplían el `start_period` del healthcheck a 60 s por la carga inicial de Oracle.
- `compose.mock.yaml` es la alternativa sin el lab: fija `INTEGRATION_MODE=mock` y se suma a cualquiera de los dos.

Al probarlo de punta a punta aparecieron fallas de producción que ya estaban en `main` y se corrigieron en el mismo cambio:

- `tsc` y `ng build` no podían crear `dist/` porque `COPY --chown` no cambia el dueño de `/app/server` ni de `/app/web` (los creó root): se agrega `chown` del directorio antes de `USER node`.
- Nginx arrancaba sin `server` en 8080: la imagen omite la plantilla si `NGINX_ENVSUBST_OUTPUT_DIR` (`/tmp/conf.d`, en un tmpfs vacío) no existe. Lo crea `web/nginx/05-crear-conf-dir.sh`; `.gitattributes` fuerza LF en los `.sh`.
- La regex de los assets con hash (`{8,}`) iba sin comillas y Nginx no la aceptaba.
- Con SSR configurado, Angular deja el shell en `index.csr.html` y Nginx servía su página de bienvenida: el Dockerfile borra el contenido por defecto y renombra el shell a `index.html`.
- Docker no publica puertos de contenedores que solo están en una red `internal`: Nginx se suma a `portal_docente_public`; la API no publica puertos (está en la red interna y en `portal_docente_egress`).

Además, en los dos Compose se pasa `LLM_ENVIAR_PERFIL` (`MAPA_MODE` se quitó el 2026-10-07, ver ADR-008), y Nginx acepta hasta 5 MB en `/api/` (igual que `express.json`), porque con 1 MB los títulos con PDFs devolvían 413 solo en Docker. `proxy_read_timeout` sube a 60 s para el asistente con LLM.

## Referencias

- `src/server/Dockerfile`, `src/web/Dockerfile`, `src/web/nginx/`
- `src/compose.yaml`, `src/compose.dev.yaml`, `src/compose.mock.yaml`
- Guía de operación: [`src/README.md#docker`](../../src/README.md#docker)

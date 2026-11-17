# ADR-028: Persistencia de los datos del portal en PostgreSQL

**Estado:** Aceptado  
**Fecha:** 2026-10-13  
**Relacionado:** ADR-003 (Oracle), ADR-004 (monolito modular), ADR-021 (Docker), ADR-026 (datos del lab por defecto)

## Contexto

El portal lee del lab Oracle (padrón, catálogo, alcance de títulos, vacantes, LOM y alertas), pero lo que carga el usuario —legajo con sus PDF, Formulario C, revisión del vocal, intereses del mapa, notificaciones leídas y preferencias de aviso— se guardaba en un archivo JSON por máquina (`.data/dev-store.json`). Eso alcanzaba para desarrollar, pero no para el ciclo completo: dos navegadores contra dos API distintas no ven los mismos datos, los PDF viajan en base64 dentro del mismo archivo y un corte a mitad de escritura puede dejarlo inconsistente.

El lab Oracle es de **solo lectura** por acuerdo con la cátedra y la provincia: el portal no crea tablas ni escribe en él.

## Opciones evaluadas

| Opción | Por qué sí | Por qué no |
|--------|-----------|------------|
| Seguir con el archivo JSON | Sin infraestructura nueva | Un archivo por máquina; sin transacciones; los PDF inflan el archivo; no sirve para probar con varios usuarios |
| Escribir en el lab Oracle | Es la base objetivo (ADR-003) | Prohibido por el acuerdo de solo lectura |
| Tablas propias `PD24_` en el lab | Mismo motor que producción | Requiere permisos que el lab no da hoy; queda como consulta al JTP |
| PostgreSQL administrado (Supabase) | Una base compartida por todo el equipo sin instalar nada | Servicio externo; no apto para datos reales de docentes sin acuerdo con la provincia |
| PostgreSQL propio en Docker | Transacciones, `BYTEA` para los PDF, mismo Compose que ya usa el equipo (ADR-021) | Un servicio más para levantar |

## Decisión

- La persistencia queda detrás de una única interfaz, `PersistenciaUsuarios` (`server/src/store/persistencia/`), con tres implementaciones: **PostgreSQL**, **archivo JSON** y **memoria** (tests).
- `PERSISTENCIA=postgres` usa la base de `DATABASE_URL`. Cualquier PostgreSQL sirve: el servicio `db` de los Compose o una base administrada como Supabase (`?sslmode=require`).
- `PERSISTENCIA=archivo` sigue disponible para `npm run dev` sin base; los tests usan siempre memoria.
- Los Compose levantan el servicio `db` (PostgreSQL 16) y la API usa `postgres` por defecto; el volumen conserva los datos entre reinicios.
- El esquema se versiona en `server/db/migraciones/` y se aplica solo al arrancar la API (tabla `schema_migrations`).
- La API carga todo en memoria al iniciar y escribe cada cambio en una transacción, en orden. Los PDF se guardan en `antecedente_adjunto` como `BYTEA`, con un máximo de 1 MB por archivo.
- `npm run db:importar --workspace=server` copia un `dev-store.json` existente a la base.
- `/api/health` informa el tipo de persistencia, si está lista y si hay escrituras pendientes o con error.

## Consecuencias

**Positivas**

- Dos navegadores y dos vocales trabajan sobre los mismos datos, y un reinicio de la API no pierde nada.
- Cambiar de base (Docker, Supabase o tablas `PD24_` si el lab las habilita) no toca los servicios del portal: solo la implementación de la interfaz.
- El lab Oracle sigue sin recibir escrituras.

**Negativas**

- Hay que levantar el servicio `db` o definir `DATABASE_URL`; con `PERSISTENCIA=postgres` y sin URL la API no arranca y lo indica.
- Los datos se cargan en memoria al iniciar: alcanza para el volumen del MVP, no para el padrón completo de docentes.
- ADR-003 sigue vigente como objetivo institucional; esta base guarda solo lo que carga el portal mientras el lab sea de solo lectura.

## Addendum 11/11: los PDF no se cargan en memoria

Al arrancar, la API leía el contenido de todos los PDF (`antecedente_adjunto` y `reclamo_adjunto`) y en cada guardado del legajo reescribía la sección entera, archivos incluidos. Con miles de docentes eso es memoria y escrituras de más.

- En memoria y en las respuestas de la API, un adjunto es solo metadata: nombre, tipo y tamaño. Al arrancar no se lee ninguna columna `contenido`.
- El contenido se pide cuando alguien abre o descarga el PDF, con una consulta por archivo: `GET /api/legajo/{id}/adjunto?ruta=…` (docente), `GET /api/junta/aspirantes/{cuil}/antecedentes/{id}/adjunto` y `GET /api/junta/reclamos/{cuil}/{id}/adjuntos/{orden}` (vocal, con los mismos permisos que el legajo y el reclamo).
- Al guardar, un adjunto que llega con contenido es nuevo y se inserta; uno sin contenido ya estaba y no se toca; los que dejaron de estar se borran. Los antecedentes y reclamos se actualizan en el lugar (`ON CONFLICT … DO UPDATE`) en vez de borrarse y volver a insertarse.
- `PERSISTENCIA=archivo` y la memoria de los tests usan una implementación simple: guardan el PDF en su propio almacenamiento (el `dev-store.json` no cambia de formato) y lo devuelven al pedirlo.
- El front pide el PDF al abrir la vista previa o descargar. El docente ve sus PDF guardados en la vista previa dentro del portal, como el vocal, en lugar de una pestaña nueva.

**Negativa:** abrir un PDF es un pedido más a la API. Sigue pendiente sacar los PDF de la base a un almacenamiento de archivos si el volumen lo pide.

## Addendum 04/11: catálogos de referencia en PostgreSQL

Los rubros del legajo (Dto. 257/19, con ítem y subítem de la Junta) y los cursos certificados en SIPEC son datos reales que no están en el lab: el Ministerio entregó los cursos como Excel. Se leían de JSON versionados en `src/fixtures/`, algo que no sostiene un despliegue real.

- La migración `008_catalogos.sql` crea `rubro_antecedente`, `rubro_antecedente_tipo` y `curso_sipec`. Con `PERSISTENCIA=postgres` la API los lee al arrancar; si las tablas están vacías, las carga una vez desde los JSON, que quedan como semilla.
- `npm run catalogos:importar --workspace=server` reemplaza el catálogo en una transacción, después de regenerarlo desde el Excel.
- Los adaptadores reciben los datos ya cargados, sin saber de dónde vienen: si el Ministerio publica los cursos en Oracle, se reemplaza solo la carga.
- Con `PERSISTENCIA=archivo` (desarrollo sin base) se siguen leyendo de los JSON.

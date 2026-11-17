# ADR-036: Períodos de la convocatoria desde el portal

**Estado:** Aceptado. Quién cambia los períodos lo reemplaza el ADR-038: ahora es el administrador de la Junta, no el permiso 2.
**Fecha:** 2026-11-10
**Relacionado:** historia «Completar Formulario C» (ventana de inscripción), ADR-031 (ingreso y rol del vocal), ADR-035 (reclamos)

## Contexto

La inscripción a cargos y horas y el período de reclamos se abrían y cerraban desde el `.env` del servidor (`INSCRIPCION_ABIERTA`, `RECLAMOS_DESDE`, `RECLAMOS_HASTA`): para cambiarlos había que editar el archivo y reiniciar la API. En producción eso lo tiene que poder hacer la Junta, pero no cualquier vocal.

## Decisión

1. Cada período se define con un modo: `ABIERTO`, `CERRADO` o `FECHAS` (del día `desde` al día `hasta`, los dos incluidos).
2. Lo guarda un vocal desde la pantalla **Junta → Períodos** (`GET` y `PUT /api/junta/periodos`). Todos los vocales lo ven; solo lo cambian los que tienen **permiso 2** («todas las opciones») en `T_USUARIOS_JUNTA`, que se lee al arrancar junto con los vocales. El resto recibe 403.
3. Lo guardado manda sobre el `.env`, que queda como valor inicial hasta que alguien lo cambie desde el portal.
4. Se guarda en PostgreSQL (migración `013_periodos_convocatoria.sql`) con quién y cuándo lo cambió, y el cambio queda en la auditoría de la Junta (ADR-032).
5. El Formulario C, los reclamos del docente, el asistente y `/api/health` leen el período vigente en lugar del `.env`.

## Consecuencias

- La Junta abre y cierra los períodos sin depender de quien administra el servidor.
- Sin el lab (`INTEGRATION_MODE=foto` o `mock`) nadie tiene permiso 2: los períodos se ven pero no se cambian. En los tests, el permiso sale del campo `permisoJunta` de los usuarios de ejemplo.
- Los vocales agregados a mano con `VOCALES_CUIL` no figuran en la tabla, así que no tienen permiso 2.
- Falta confirmar con la Junta si son los vocales de permiso 2 quienes abren y cierran la convocatoria (lista de preguntas para el vocal).

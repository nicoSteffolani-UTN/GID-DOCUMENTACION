# ADR-038: Administrador de la Junta

**Estado:** Aceptado
**Fecha:** 2026-11-14
**Relacionado:** ADR-018 (reparto de escuelas base), ADR-036 (períodos de la convocatoria), reunión con el vocal del 2026-11-14

## Contexto

En la reunión, el vocal contó que la Junta tiene un rol por encima del vocal común (lo llamó «supravocal») que puede hacer todo lo demás. Pidió dos cosas para ese rol:

- Abrir y cerrar los períodos de inscripción y de reclamos. Hasta ahora lo hacían los vocales con permiso 2 de `T_USUARIOS_JUNTA` (ADR-036).
- Corregir a mano el bolsón de escuelas base de un vocal: pasarle algunas escuelas a otro (por ejemplo cuando se desbalancea la carga o un vocal se va en plena convocatoria). El reparto automático le parece bien, pero tiene que poder corregirse.

No sabemos si ese rol existe en la base de la Junta, ni si coincide con el permiso 2. El grupo decidió crearlo en el portal.

## Decisión

1. El portal tiene un rol **administrador de la Junta**. Los administradores se configuran en el `.env` con `ADMINISTRADORES_CUIL` (CUIL separados por coma). En los tests salen del campo `administrador` de los usuarios de ejemplo.
2. Un administrador entra a las pantallas de la Junta aunque no sea vocal. Si no figura como vocal en `T_USUARIOS_JUNTA` ni en `VOCALES_CUIL`, no recibe escuelas en el reparto automático.
3. Los períodos los cambia solo el administrador. El permiso 2 de `T_USUARIOS_JUNTA` deja de habilitarlo.
4. La pantalla **Junta → Asignación** (solo para administradores) lista cada vocal con **todo su bolsón** del reparto del padrón (escuelas con o sin aspirantes hoy). El flujo principal es marcar escuelas y pasarlas a otro vocal, con confirmación. El enlace del menú solo lo ve el administrador. El endpoint de intercambiar bolsones enteros queda por si hace falta un caso extremo, pero no es la acción de la pantalla.
5. Cada escuela movida a mano se guarda con el vocal que la recibe, quién la movió y cuándo (migración `014_reasignacion_escuela.sql`). Ese cambio manda sobre el reparto automático y sobre la asignación configurada: el vocal que la recibe pasa a revisar sus legajos y a resolver sus reclamos.
6. Las acciones quedan en la auditoría de la Junta, como el resto de los cambios del módulo.

## Consecuencias

- La Junta corrige el reparto sin depender de quien administra el servidor, pero para nombrar a un administrador hay que editar el `.env` y reiniciar la API.
- Un vocal de la Junta que todavía no entró al portal aparece en la pantalla por su DNI: el portal solo conoce el nombre de quienes ingresaron.
- Si más adelante se confirma que el rol existe en la base de la Junta, alcanza con cambiar de dónde sale la lista de administradores (`modules/administracion/administradores.ts`).

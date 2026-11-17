# ADR-018: Vocal × escuela — pendiente Oracle lab

**Estado:** Aceptado con reparto automático entre los vocales reales (la Junta asigna a mano; a confirmar con ella)  
**Fecha:** 2026-08-24  
**Relacionado:** US-06, ADR-014, diagrama 19

## Contexto

US-06 asigna escuelas a vocales vía `VocalEscuelaRepository` (`vocal-escuelas.mock.json`: CUIL vocal → `escuelaId[]`). Se buscó vista acta en Oracle lab.

## Discovery MT4D (24/08)

| Objeto | Hallazgo |
|--------|----------|
| Vistas con `VOCAL`, `ACTA`, `ASIGNACION` | No hay acta CUIL×`ID_EMPRESA` usable |
| `T_USUARIOS_JUNTA` | Permisos de sistema, no asignación escuela–vocal |

## Decisión

**No implementar** `OracleVocalAdapter` en lab. Mantener `MockVocalEscuelasAdapter` en todos los modos hasta vista ministerial confirmada.

## Consecuencias

- Demo junta/vocal en prod Oracle **no** refleja asignaciones reales de acta.
- Roles `vocal` en JWT siguen funcionando con fixture local.

## Actualización (13/10)

El fixture usa escuelas ficticias (`ESC-…`) que no existen en el padrón del lab. En `prod` la API ya no lo usa: las escuelas reales con inscripciones se reparten entre los vocales de forma determinística (la misma escuela siempre cae en el mismo vocal), y la junta lo avisa como «asignación de escuelas de ejemplo». En `mock` y en los tests se mantiene el fixture.

## Actualización (25/10)

En la revisión del 23/10 el docente inscribió a un aspirante y entró con un vocal de prueba que no tenía esas escuelas: el reparto determinístico las había dejado en otro vocal, así que no pudo seguir el circuito inscripción → valoración. Las escuelas con inscripciones que no figuran en una asignación configurada ahora las ven **todos** los vocales. Las escuelas con asignación configurada siguen siendo exclusivas de su vocal.

En el sistema original de la Junta el vocal valora por **escuelas base asignadas**, y la escuela base la elige el aspirante en la inscripción. Cuando se conozca esa asignación, reemplaza a esta regla provisoria.

**Addendum (25/10, más tarde):** la regla de «todos los vocales» se reemplaza por el reparto por **escuela base**, como en el sistema original. La escuela base la elige el aspirante en el Formulario C (addendum del 27/10 de ADR-013) y define el único vocal que revisa cada legajo, que ve toda la inscripción del aspirante. En secundaria las escuelas base son las del padrón: en 2019 hubo 1.170 bases distintas (`T_INSCRIPCIONES_ESCUELABASE_JD`), todas también escuelas de inscripción, y el 84 % de los docentes eligió como base una de sus escuelas. `T_ESCUELAS_BASE` es un catálogo de los niveles 1 y 5, no de secundaria. Por eso las escuelas del padrón sin asignación configurada se reparten **fijas y parejas** entre los vocales (ordenadas, de a una por vocal), y las inscripciones no mueven el reparto; cada vocal ve solo las suyas que son base de algún aspirante. Mientras se prueba, con `DOCENTE_PRUEBA_*` y `VOCAL_PRUEBA_*` configurados, el legajo del docente de prueba lo revisa siempre el vocal de prueba.

## Addendum (26/10/2026): respuesta del DBA

La Junta asigna las escuelas base a los vocales **a mano**: no hay tabla, vista ni proceso que la registre, ni en el lab ni en producción. Lo confirma lo relevado en 2019: `ID_USUARIO_MODIF` de `T_INSCRIPCIONES_JD` muestra un solo vocal de medio en 855 de 861 escuelas base, que es una carga masiva y no una asignación. Por eso:

- No hay `OracleVocalAdapter` que escribir: la pregunta del acta de vocal queda respondida.
- El reparto fijo y parejo del addendum anterior, y la regla que deja al docente de prueba con el vocal de prueba, siguen siendo **provisorios**. El reparto se calcula en cada consulta (`JuntaService.asignacionesEfectivas()`) y no se guarda.
- La salida coherente es que la asignación también sea manual en el portal: una pantalla o un archivo donde la Junta reparta las escuelas base entre sus vocales, que alimente `VocalEscuelaRepository`. Se lleva a la reunión con la cátedra antes de programarla.
- Ojo con la regla de prueba: mueve la **escuela base entera** al vocal de prueba, así que todos los aspirantes con esa base pasan a él.

## Addendum (06/11/2026): con usuarios reales de CiDi el reparto no alcanza

Al revisar lo que corre en producción apareció que el reparto solo conoce a los vocales de `auth.listUsuarios()`, y con `AUTH_MODE=cidi` esa lista no los trae: se identifican recién al entrar y `T_USUARIOS_JUNTA` los guarda por DNI, no por CUIL. **Un vocal real entraría y no vería ninguna escuela.** No se resolvió sumando a los vocales a medida que entran, porque el reparto cambiaría con cada ingreso y dejaría de ser fijo.

Se resolvió con el reparto automático del addendum siguiente, en lugar de la carga manual.

Lo que sí se corrigió para usuarios reales: el aspirante se lista por su Formulario C aunque no tenga antecedentes, y el vocal ve el nombre con que entró por CiDi (tabla `usuario_portal`, migración 010). Sin nombre guardado se muestra el CUIL enmascarado, nunca el completo.

La nota de la asignación provisoria pasó de un aviso fijo a una ventana que abre el botón «Cómo se asignan las escuelas», como pidió la revisión del 23/10.

## Addendum (06/11/2026, más tarde): reparto automático entre los vocales reales

Se descarta la carga manual: el portal reparte solo, con datos reales, y nadie tiene que cargar nada.

- **Vocales:** al arrancar se leen de `T_USUARIOS_JUNTA` los activos de secundaria con permiso 1 o 2, el mismo criterio del ingreso. Se identifican por DNI, porque la tabla no guarda CUIL. Se suman los de `VOCALES_CUIL`, que en la versión sin Oracle son la única fuente.
- **Carga:** cada escuela pesa lo que pesó en la última convocatoria con más de 10.000 inscripciones. Se reparte de la más grande a la más chica, siempre al vocal con menos carga. Una escuela sin datos cuenta como uno.
- **Fijo:** se guarda en `vocal_escuela` (migración 011). Una escuela nueva o un vocal nuevo no mueven lo asignado, y la baja de un vocal solo reparte sus escuelas. Un vocal nuevo recibe únicamente lo que quedó sin dueño.
- **Si Oracle falla al arrancar:** la lista queda vacía y el reparto guardado no se toca. Tampoco se reparte entre los de `VOCALES_CUIL`, porque se llevarían todas las escuelas.
- **Limitación:** los vocales se leen al arrancar, así que un alta en la tabla se ve después del próximo reinicio.

Con los datos reales (61 vocales, 1.165 escuelas del padrón, cantidades de 2019) cada vocal queda con 983 a 986 aspirantes y 16 a 20 escuelas. El reparto anterior, de a una escuela por vocal, daba de 621 a 1.606.

Queda afuera un vocal activo cuyo usuario no tiene la forma `A` + DNI: tampoco lo reconoce el ingreso. Es una consulta para la Junta.

**Sigue siendo una decisión del portal:** la Junta hoy reparte a mano y puede tener otro criterio. Hay que confirmarlo con ella. Si hace falta, se suma una forma de corregir casos puntuales sobre este mismo reparto.

La regla que deja al docente de prueba con el vocal de prueba sigue vigente mientras existan esas cuentas: en producción no deben configurarse.

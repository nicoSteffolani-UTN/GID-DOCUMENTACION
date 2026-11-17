# ADR-031: Ingreso con Ciudadano Digital

**Estado:** Aceptado  
**Fecha:** 2026-10-21  
**Relacionado:** ADR-018 (vocal × escuela), ADR-029 (tablas de la Junta)

## Contexto

US-01 pide que el docente ingrese con su cuenta de Ciudadano Digital (CiDi) de nivel 2 o superior. Hasta ahora el portal usaba usuarios de ejemplo. Ciudadano Digital entregó credenciales de aplicación para el ambiente de prueba y para producción.

El vocal también ingresa con CiDi; el portal tiene que saber si la persona es vocal de la Junta de secundaria.

## Decisión

1. `AUTH_MODE` define el ingreso: `mock` (usuarios de ejemplo, por defecto) o `cidi`. En `cidi`, el ingreso con usuarios de ejemplo responde 403.
2. Flujo:
   - El botón «Ingresar con CiDi» lleva a `GET /api/auth/cidi/ingresar`, que redirige a la pantalla de ingreso de CiDi con el id de la aplicación (`app`) y la dirección de retorno (`url`, desde `CIDI_URL_RETORNO`).
   - CiDi devuelve al usuario a `GET /api/auth/cidi/retorno` con su cookie de sesión.
   - La API consulta `Usuario/Obtener_Usuario_Aplicacion` con el id, la contraseña de la aplicación, el hash de la cookie, la marca de tiempo (`yyyyMMddHHmmssfff`) y el token de la llamada (SHA-1 de la marca de tiempo más la clave; el entorno de prueba de CiDi acepta SHA-1 y SHA-512).
   - Con nivel 2 o más emite el JWT del portal y lo pasa a la pantalla `/login/cidi` en el fragmento de la URL (`#token=`), que el navegador no envía a ningún servidor. Con nivel menor, vuelve al login con el mensaje de US-01.
3. Rol: es vocal quien figura en `T_USUARIOS_JUNTA` como `A` + DNI, con nivel educativo 2 y permiso 1 (vocal) o 2 (todas las opciones; addendum 25/10: el vocal que más legajos validó en el lab tiene permiso 2). `VOCALES_CUIL` suma vocales a mano. El resto ingresa como aspirante.
4. La sesión se cierra en el navegador después de 30 minutos sin actividad.
5. Las credenciales de la aplicación (`CIDI_ID_APP`, `CIDI_KEY_APP`, `CIDI_PASSWORD`) van solo en el `.env` del servidor. Las URL de prueba son el valor por defecto; en producción se configuran `CIDI_LOGIN_URL` y `CIDI_API_URL`.

## A confirmar con Ciudadano Digital

- El servicio y los campos de la respuesta siguen la documentación pública de la API de cuentas de CiDi. Falta confirmar con un ingreso real qué valores toma `Id_Estado`, que el portal usa como nivel de la cuenta.
- La cookie de CiDi pertenece al dominio `.cba.gov.ar`: el portal tiene que publicarse bajo ese dominio para recibirla. Mientras tanto, el retorno también acepta el hash en el parámetro `cidi`.

## Consecuencias

- El ingreso real depende de que el portal esté publicado en el servidor oficial.
- El modo `mock` sigue disponible para desarrollo y pruebas automáticas.

## Addendum (05/11/2026): sin usuarios de ejemplo

Con `AUTH_MODE=mock` el login ofrece solo las cuentas reales del lab configuradas en el `.env` del servidor (`DOCENTE_PRUEBA_*`, `VOCAL_PRUEBA_*`), en cualquier `INTEGRATION_MODE`, incluida la versión de demostración (`foto`). Los usuarios de `fixtures/usuarios.mock.json` y la semilla `fixtures/demo-store.seed.json` quedan solo para los tests, y la API arranca sin semilla. `GET /api/auth/cuenta-prueba` deja de informar `ejemplos`. Se elimina `fixtures/demo-store.prod.seed.json`.

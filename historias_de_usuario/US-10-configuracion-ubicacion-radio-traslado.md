# [ID: US-10] Configuración de ubicación de referencia y radio de traslado preferido en el perfil

- **Épica:** E02 - Perfil Docente, Trayectoria y Antigüedad (SGE / PeopleNet)
- **Prioridad:** P2 (Media)
- **Estimación:** 3 SP
- **Rol / Actor:** Docente Aspirante

## Descripción (Narrativa)
> **Como** docente aspirante,  
> **quiero** configurar en mi perfil mi localidad/barrio de referencia y el radio máximo de distancia en kilómetros que estoy dispuesto a trasladarme,  
> **para** que las herramientas de apoyo a la decisión, el mapa interactivo y las alertas de vacantes prioricen los establecimientos educativos cercanos a mi domicilio.

## Reglas de Negocio y Conexiones Externas
- **Regla de Negocio:** Parámetro de preferencia personal utilizado por el módulo de Ayudas para Decidir y filtro de radio de viaje (ADR-025) sin alterar el domicilio legal de CiDi.
- **Persistencia:** Base de datos del portal (PostgreSQL / store de preferencias del usuario, ADR-028).

## Criterios de Aceptación
1. **Debe** permitir al docente seleccionar o actualizar su ubicación geográfica de referencia (departamento/localidad o coordenadas de referencia) y su radio de viaje preferido en kilómetros (ej. 5 km, 10 km, 25 km, 50 km).
2. **Debe** validar que el radio de viaje ingresado sea un valor numérico positivo dentro de los rangos razonables de traslado provincial (entre 1 km y 500 km).
3. **Debe** persistir la preferencia del usuario en la base de datos del portal para que se mantenga entre sesiones.
4. **Debe** poner esta configuración a disposición del recomendador de escuelas del Formulario C, las vacantes sugeridas y el mapa territorial para calcular distancias en línea recta / radio de cobertura.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar configurar un radio de viaje de 15 km en el perfil, guardar los cambios y verificar que al recargar la página la preferencia permanece almacenada. *(Pasa)*
- **CP-02:** Probar ingresar un radio de viaje inválido (valor negativo, cero o texto) y verificar que el sistema impide el guardado mostrando el mensaje de validación. *(Falla controlada)*
- **CP-03:** Probar que al actualizar el radio de viaje en el perfil, las sugerencias de escuelas por cercanía reflejen el nuevo umbral de distancia configurado. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Baja | CRUD de preferencias de usuario y cálculo trigonométrico de distancia (fórmula de Haversine) contra coordenadas de escuelas. |
| **Esfuerzo** | Bajo | Formulario de preferencia en perfil y persistencia en tabla/store de usuario. |
| **Incertidumbre** | Nula | Coordenadas de escuelas disponibles en el padrón georreferenciado del sistema. |

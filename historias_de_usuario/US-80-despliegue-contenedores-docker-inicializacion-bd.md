# [ID: US-80] Despliegue reproducible en contenedores Docker e inicialización automática de esquemas y datos semilla

- **Épica:** Sin Épica (Transversal - Operación e Interoperabilidad)
- **Prioridad:** P1 (Alta / MVP)
- **Estimación:** 5 SP
- **Rol / Actor:** Administrador de Infraestructura / Equipo de Desarrollo

## Descripción (Narrativa)
> **Como** ingeniero de despliegue o administrador del entorno,  
> **quiero** levantar la totalidad de la plataforma (frontend Angular, backend Node.js y base de datos PostgreSQL con sus esquemas y semillas iniciales) mediante definición declarativa de contenedores Docker,  
> **para** garantizar despliegues reproducibles, ágiles y libres de diferencias de configuración entre los ambientes de desarrollo, prueba y producción.

## Reglas de Negocio y Conexiones Externas
- **Gestión de la Configuración y Despliegue (`SCM.md`, `compose.dev.yaml`, `compose.mock.yaml`, `ADR-007`, `ADR-028`):** Orquestación con Docker Compose, inicialización idempotente de tablas en PostgreSQL y pipeline de Integración Continua en GitHub Actions.

## Criterios de Aceptación
1. **Debe** proveer archivos de orquestación (`compose.dev.yaml` y `compose.mock.yaml`) que levanten de forma coordinada la base de datos PostgreSQL y los servicios de la aplicación.
2. **Debe** ejecutar de manera automática e idempotente (`CREATE TABLE IF NOT EXISTS`) la creación de las tablas operativas y de auditoría en PostgreSQL al iniciar el servidor si aún no existen.
3. **Debe** cargar los datos semilla iniciales de prueba únicamente cuando las tablas se encuentren vacías, preservando los datos existentes en reinicios posteriores del contenedor.
4. **Debe** validar en el pipeline de Integración Continua (GitHub Actions) la compilación en modo estricto de TypeScript y la ejecución de las suites de pruebas automatizadas ante cada *push* o *Pull Request*.

## Casos de Prueba de Usuario (Aceptación)
- **CP-01:** Probar levantar el entorno desde cero mediante Docker Compose y verificar que se inicializa la base de datos PostgreSQL, se crean las tablas y el portal responde en el puerto configurado. *(Pasa)*
- **CP-02:** Probar reiniciar los contenedores luego de haber cargado un antecedente y confirmado un Formulario C, verificando que los datos persisten en el volumen de PostgreSQL sin sobrescribirse. *(Pasa)*

## Matriz de Estimación Técnica
| Dimensión | Nivel | Justificación Técnica |
| :--- | :---: | :--- |
| **Complejidad** | Media | Configuración de contenedores multi-servicio, redes, volúmenes persistentes y scripts de migración/seed idempotentes. |
| **Esfuerzo** | Medio | Definición de `Dockerfile`, `compose.*.yaml`, inicializador SQL y workflows de CI en `.github/workflows`. |
| **Incertidumbre** | Nula | Documentado en el Manual de Instalación y Operación (Instancia 5 §3) y en `SCM.md`. |

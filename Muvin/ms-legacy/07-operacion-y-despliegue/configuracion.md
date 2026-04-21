# Configuración del microservicio

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21
> **Fuente:** `src/config/environments.ts` (validación Joi)

## Variables de entorno

| Variable | Tipo Joi | Tipo TS | Requerida | Default docker-compose | Descripción |
|----------|----------|---------|-----------|------------------------|-------------|
| `LEGACY_MICROSERVICE_HOST` | `string` | `string` | ✅ | `0.0.0.0` | Host de escucha del listener TCP |
| `LEGACY_MICROSERVICE_PORT` | `number` | `number` | ✅ | `4001` | Puerto TCP del microservicio |
| `LEGACY_MICROSERVICE_TRANSPORT` | `number` | `number` | ✅ | `0` | Enum de transporte NestJS. TCP = 0 |
| `LEGACY_MICROSERVICE_CMD` | `string` | `string` | ✅ | `CMD-LEGACY` | Identificador del canal TCP (`{ cmd }`) |
| `LEGACY_MICROSERVICE_BASE_URL` | `string` (URI) | `string` | ✅ | `https://dev.muvinapp.com/api/backend/web/` | URL base del backend legacy REST |

### Comportamiento de validación

- Si alguna variable falta o es del tipo incorrecto, el proceso **termina con error descriptivo** antes de activar el listener TCP.
- `LEGACY_MICROSERVICE_BASE_URL` es validado como URI Joi (no solo string).
- `LEGACY_MICROSERVICE_TRANSPORT` debe ser un valor válido del enum `Transport` de NestJS.

### Advertencia sobre `LEGACY_MICROSERVICE_BASE_URL`

> [!warning]
> El docker-compose define `https://dev.muvinapp.com/api/backend/web/` como valor por defecto. En producción, siempre sobreescribir con la URL del ambiente correcto. Un deploy sin esta variable en producción enviaría tráfico al ambiente de desarrollo.

## Configuración de HttpModule

Definida en `src/module.ts`. No es configurable por variables de entorno (valores hardcodeados):

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| `timeout` | `5000` ms | Timeout de las peticiones HTTP al backend legacy |
| `maxRedirects` | `5` | Máximo de redirects a seguir |

> [!note] Configuración por query
> No hay timeout por query. Todas las peticiones al backend legacy tienen el mismo timeout de 5 segundos.

## Configuración del ValidationPipe (global)

Definida en `src/main.ts`. No configurable por variables de entorno:

| Opción | Valor | Descripción |
|--------|-------|-------------|
| `whitelist` | `true` | Elimina del payload campos no declarados en el DTO |
| `forbidNonWhitelisted` | `true` | Lanza error si el payload contiene campos extra |

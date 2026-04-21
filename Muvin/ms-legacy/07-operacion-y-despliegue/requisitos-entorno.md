# Requisitos de entorno

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Runtime

| Requisito | Versión mínima | Versión recomendada | Notas |
|-----------|---------------|---------------------|-------|
| Node.js | 20.x | **22.x LTS** | Node 20 alcanza EOL en abril 2026 |
| npm | 10.x | 10.x | Incluido con Node 22 |
| Docker | 24.x | 27.x | Para despliegue en contenedor |
| Docker Compose | v2.x | v2.x | Para entorno local |

## Variables de entorno requeridas

Todas las siguientes variables son **obligatorias**. El proceso no arranca si alguna falta o tiene tipo incorrecto (validación Joi al inicio).

| Variable | Tipo | Descripción | Valor por defecto (docker-compose) |
|----------|------|-------------|-------------------------------------|
| `LEGACY_MICROSERVICE_HOST` | `string` | Host en el que el microservicio escucha | `0.0.0.0` |
| `LEGACY_MICROSERVICE_PORT` | `number` | Puerto TCP del microservicio | `4001` |
| `LEGACY_MICROSERVICE_TRANSPORT` | `number` | Enum de transporte NestJS (TCP = 0) | `0` |
| `LEGACY_MICROSERVICE_CMD` | `string` | Identificador del canal TCP | `CMD-LEGACY` |
| `LEGACY_MICROSERVICE_BASE_URL` | `string` (URI válida) | URL base del backend legacy REST | `https://dev.muvinapp.com/api/backend/web/` |

> [!warning] Valor por defecto de `LEGACY_MICROSERVICE_BASE_URL`
> El valor por defecto apunta al ambiente de **desarrollo**. En producción esta variable debe ser sobreescrita explícitamente.

## Red Docker

El contenedor requiere acceso a la red externa `muvin-network` (tipo `external: true`). Esta red debe existir en el host Docker antes de levantar el contenedor.

```bash
docker network create muvin-network
```

## Puertos expuestos

| Puerto | Protocolo | Descripción |
|--------|-----------|-------------|
| `4001` | TCP | Listener NestJS para mensajes del ecosistema |

## Archivo `.env` (desarrollo local)

El proyecto usa `dotenv`. En desarrollo local, crear un `.env` en la raíz del proyecto:

```dotenv
LEGACY_MICROSERVICE_HOST=0.0.0.0
LEGACY_MICROSERVICE_PORT=4001
LEGACY_MICROSERVICE_TRANSPORT=0
LEGACY_MICROSERVICE_CMD=CMD-LEGACY
LEGACY_MICROSERVICE_BASE_URL=https://dev.muvinapp.com/api/backend/web/
```

> [!note]
> No commitear el `.env`. Está correctamente en `.gitignore`.

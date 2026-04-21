# Módulo: Config / Entorno

> **Ruta/Namespace:** `src/config/`
> **Criticidad:** 🔴 Alta
> **Estado:** Activo

## Propósito

Carga, valida y exporta las variables de entorno requeridas por el microservicio. Usa **Joi** para validar el schema al momento del arranque: si alguna variable requerida está ausente o tiene el tipo incorrecto, el proceso falla inmediatamente con un mensaje descriptivo.

Adicionalmente, valida que el valor de `LEGACY_MICROSERVICE_TRANSPORT` sea un valor válido del enum `Transport` de NestJS.

## Variables de entorno

| Variable | Tipo | Obligatoria | Descripción |
|----------|------|-------------|-------------|
| `LEGACY_MICROSERVICE_HOST` | `string` | Sí | Host donde escucha el microservicio TCP |
| `LEGACY_MICROSERVICE_PORT` | `number` | Sí | Puerto TCP (default Docker: `4001`) |
| `LEGACY_MICROSERVICE_TRANSPORT` | `number` | Sí | Enum Transport de NestJS (solo `0` = TCP es válido en el código) |
| `LEGACY_MICROSERVICE_CMD` | `string` | Sí | Identificador del MessagePattern (default Docker: `CMD-LEGACY`) |
| `LEGACY_MICROSERVICE_BASE_URL` | `string` | Sí | URL base del backend legacy REST |

## Validación de transporte

La función `transportFn` verifica que el valor de `LEGACY_MICROSERVICE_TRANSPORT` pertenezca al enum `Transport` de NestJS. Si el valor es inválido, `errFn` lanza un error.

> [!warning] Lógica de validación invertida
> La función `transportFn` retorna `true` cuando el valor es **inválido** (no coincide con ningún transporte conocido). El nombre sugiere lo contrario. Esto es un code smell de legibilidad, aunque funcionalmente correcto. Ver [[deuda-tecnica]].

## Dependencias

- **Depende de:** `joi`, `dotenv`, `@nestjs/microservices` (Transport enum)
- **Es usado por:** [[modulo-controller]], [[modulo-service]], `src/main.ts`

## Archivos fuente relevantes

- `src/config/environments.ts`
- `src/config/_index.ts`

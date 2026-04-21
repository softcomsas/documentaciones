# Requisitos de Entorno

> **Contexto:** [[build-y-despliegue]] · [[configuracion]]

## Runtime

| Requisito | Versión mínima | Notas |
|-----------|---------------|-------|
| Node.js | 22.x | `@types/node ^22` en devDependencies |
| npm | 10.x | Incluido con Node 22 |
| MySQL | 8.0 | Docker image `mysql:8.0` en docker-compose |

## Variables de entorno (todas requeridas)

> [!warning] No existe `.env.example` en el repositorio
> Se debe crear manualmente. Ver plantilla abajo.

| Variable | Tipo | Ejemplo | Descripción |
|----------|------|---------|-------------|
| `LOGS_MICROSERVICE_HOST` | string | `0.0.0.0` | Host de escucha del microservicio TCP |
| `LOGS_MICROSERVICE_PORT` | number | `3002` | Puerto TCP |
| `LOGS_MICROSERVICE_TRANSPORT` | number | `0` | Enum Transport de NestJS (0 = TCP) |
| `DATABASE_URL` | string | `mysql://user:pass@host:3306/db_logs` | Cadena de conexión MySQL para Prisma |

### Valores válidos para `LOGS_MICROSERVICE_TRANSPORT`

| Valor | Transporte |
|-------|-----------|
| `0` | TCP (único validado por `environments.ts`) |
| Otros | Rechazado con error al startup |

### Plantilla `.env`

```dotenv
LOGS_MICROSERVICE_HOST=0.0.0.0
LOGS_MICROSERVICE_PORT=3002
LOGS_MICROSERVICE_TRANSPORT=0
DATABASE_URL=mysql://muvin:muvin@localhost:3306/db_logs
```

## Validación al arranque

`src/config/environments.ts` usa Joi para validar las 4 variables en bootstrap. Si alguna falta o tiene valor inválido, el proceso termina con un error descriptivo antes de inicializar NestJS.

## Notas de red

- El MS no expone HTTP — solo TCP en `LOGS_MICROSERVICE_PORT`.
- El caller debe conocer el host y puerto TCP para conectarse.
- Sin TLS ni autenticación — asumir red interna/privada.

---

*Ver también: [[build-y-despliegue]] · [[configuracion]] · [[security-inventory]]*

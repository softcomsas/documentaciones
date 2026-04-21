# Configuración del Microservicio

> **Contexto:** [[requisitos-entorno]] · [[build-y-despliegue]]

## Fuente de configuración

Todas las configuraciones se leen de variables de entorno. Se usan dos mecanismos:

| Mecanismo | Archivo | Propósito |
|-----------|---------|-----------|
| `dotenv` | Carga `.env` al inicio | Proporciona las variables |
| `Joi` (environments.ts) | Valida y parsea | Garantiza tipos correctos |

## Transport options

El transporte se configura via `LOGS_MICROSERVICE_TRANSPORT`. Solo TCP (valor `0`) está validado:

```typescript
// src/config/environments.ts
transportFn(transport: number): Transport {
  if (!Object.values(Transport).includes(transport)) {
    throw new Error(`Invalid transport: ${transport}`);
  }
  return transport;
}
```

| Valor | Transporte NestJS | Soportado |
|-------|-------------------|-----------|
| `0` | `Transport.TCP` | ✅ |
| Otros | Redis, NATS, etc. | ⚠️ No probado |

## Path aliases

Los imports usan aliases configurados en `tsconfig.paths.json`. Se resuelven en build via `tsconfig-paths`:

| Alias | Resolución |
|-------|-----------|
| `@common` | `src/common/_index.ts` |
| `@core` | `src/core/_index.ts` |
| `@config` | `src/config/_index.ts` |
| `@contract-ms-logs` | `src/contracts/ms-logs/_index.ts` |
| `@contract-ms-legacy` | `src/contracts/ms-legacy/_index.ts` |

## Configuración de Prisma

El schema de Prisma está en `prisma/schema.prisma`:

```prisma
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}
```

Después de cambiar el schema, ejecutar:
```bash
npx prisma generate    # Regenera cliente TS
npx prisma migrate dev # Crea nueva migración (dev)
```

## ValidationPipe global

Configurado en `src/main.ts`:

```typescript
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,           // Elimina campos no declarados en DTOs
  forbidNonWhitelisted: true // Lanza error si hay campos extra
}));
```

---

*Ver también: [[requisitos-entorno]] · [[build-y-despliegue]]*

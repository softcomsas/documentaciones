# Configuración

**Última actualización:** 2026-04-27

## Variables de Entorno

### Requeridas

```bash
# Host y puerto del microservicio
COMMERCIAL_MICROSERVICE_HOST=0.0.0.0          # O localhost
COMMERCIAL_MICROSERVICE_PORT=4005             # TCP port
COMMERCIAL_MICROSERVICE_TRANSPORT=0           # 0=TCP

# Conexión a MySQL
DATABASE_URL="mysql://user:password@host:port/database"
```

### Validación

Las variables se validan con Joi en `src/config/environments.ts`:

```typescript
const envVarsSchema = Joi.object({
  COMMERCIAL_MICROSERVICE_HOST: Joi.string().default('localhost'),
  COMMERCIAL_MICROSERVICE_PORT: Joi.number().default(4005),
  DATABASE_URL: Joi.string().required(),
});
```

---

## Ejemplo: .env Local

```bash
# Development
COMMERCIAL_MICROSERVICE_HOST=localhost
COMMERCIAL_MICROSERVICE_PORT=4005
COMMERCIAL_MICROSERVICE_TRANSPORT=0
DATABASE_URL="mysql://dev_user:SecurePass123@localhost:3306/dev_commercial"
NODE_ENV=development
```

---

## Ejemplo: .env Producción (Azure)

```bash
# Production
COMMERCIAL_MICROSERVICE_HOST=0.0.0.0
COMMERCIAL_MICROSERVICE_PORT=4005
COMMERCIAL_MICROSERVICE_TRANSPORT=0
DATABASE_URL="mysql://muvinapp_user:SecurePass123@muvinapp-preprod-db.mysql.database.azure.com:3306/dev_commercial"
NODE_ENV=production
```

---

## Configuración de Transport

Archivo: `src/config/transport.ts`

```typescript
export const MicroserviceOptions = {
  transport: Transport.TCP,
  options: {
    host: process.env.COMMERCIAL_MICROSERVICE_HOST,
    port: process.env.COMMERCIAL_MICROSERVICE_PORT,
  },
};
```

Usado en `main.ts`:

```typescript
app.connectMicroservice(MicroserviceOptions);
```

---

## Configuración de Prisma

Archivo: `prisma/schema.prisma`

```prisma
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}
```

El ORM lee `DATABASE_URL` automáticamente.

---

## Docker Compose (Desarrollo)

Archivo: `docker-compose.yml`

```yaml
services:
  app:
    build: .
    ports:
      - "4005:4005"
    environment:
      COMMERCIAL_MICROSERVICE_HOST: 0.0.0.0
      COMMERCIAL_MICROSERVICE_PORT: 4005
      DATABASE_URL: mysql://user:pass@db:3306/dev_commercial
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: dev_commercial
      MYSQL_USER: dev_user
      MYSQL_PASSWORD: SecurePass123
      MYSQL_ROOT_PASSWORD: root123
    ports:
      - "3306:3306"
```

---

## Secretos (En Producción)

### NO usar en código

❌ Nunca hardcodear:
```typescript
const dbUrl = "mysql://user:pass@host/db";  // ❌ MAL
```

### Usar variables de entorno

✅ Siempre usar env vars:
```typescript
const dbUrl = process.env.DATABASE_URL;  // ✅ BIEN
```

### En Kubernetes / Docker

Pasar via:
- Variables de entorno
- Secrets volume
- ConfigMap

---

## Checklist Configuración

- [ ] `.env` existe y tiene `DATABASE_URL`
- [ ] MySQL está accesible
- [ ] Puerto 4005 está libre
- [ ] `COMMERCIAL_MICROSERVICE_HOST` es `0.0.0.0` en producción
- [ ] No hay hardcoded secrets
- [ ] Prisma está sincronizado (`npx prisma generate`)

---

## Documentos Relacionados

- [[requisitos-entorno|Requisitos de Entorno]] - Setup
- [[build-y-despliegue|Build y Despliegue]] - CI/CD
- [[../00-overview/stack-tecnologico|Stack Tecnológico]] - Versiones


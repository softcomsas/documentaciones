# Stack Tecnológico

**Última actualización:** 2026-04-27

## Versiones Principales

| Tecnología | Versión | Propósito |
|-----------|---------|----------|
| **Node.js** | 20.x | Runtime de JavaScript/TypeScript |
| **NestJS** | 11.1.8 | Framework de microservicios |
| **TypeScript** | 5.9.3 | Tipado estático para JavaScript |
| **Prisma** | 6.19.0 | ORM y generador de cliente |
| **MySQL** | 8.x (Azure) | Base de datos relacional |
| **Docker** | Latest | Containerización para deploy |

---

## Dependencias Principales

### Producción (`dependencies`)

```json
{
  "@nestjs/common": "^11.1.8",          // Core NestJS
  "@nestjs/core": "^11.1.8",             // Core NestJS
  "@nestjs/microservices": "^11.1.8",   // TCP Transport
  "@prisma/client": "^6.19.0",          // ORM cliente
  "class-validator": "^0.14.x",          // Validación de DTOs
  "class-transformer": "^0.5.x",         // Transformación de DTOs
  "joi": "^17.x",                        // Validación de env vars
  "reflect-metadata": "^0.1.x"           // Metadata reflection
}
```

### Desarrollo (`devDependencies`)

```json
{
  "@nestjs/cli": "^11.x",                // CLI para generar módulos
  "@nestjs/testing": "^11.x",           // Testing utilities
  "@types/node": "^20.x",                // Tipos de Node.js
  "@types/jest": "^29.x",                // Tipos de Jest
  "@typescript-eslint/eslint-plugin": "^6.x", // Linting
  "jest": "^29.x",                       // Testing framework
  "ts-jest": "^29.x",                    // Jest + TypeScript
  "ts-loader": "^9.x",                   // TypeScript loader
  "typescript": "^5.9.3",                // Compilador TypeScript
  "prisma": "^6.19.0"                    // CLI de Prisma
}
```

---

## Arquitectura de Tecnologías

```
┌─────────────────────────────────────────┐
│     TypeScript (5.9.3)                  │
│     (Lenguaje)                          │
└─────────────────────────────────────────┘
             ▲
             │ Compila a
             ▼
┌─────────────────────────────────────────┐
│     Node.js (20.x)                      │
│     (Runtime)                           │
└─────────────────────────────────────────┘
             ▲
             │ Ejecuta
             ▼
┌─────────────────────────────────────────┐
│     NestJS (11.1.8)                     │
│     (Framework)                         │
│  ├─ Common                              │
│  ├─ Core                                │
│  └─ Microservices (TCP Transport)       │
└─────────────────────────────────────────┘
             ▲
             │ Usa
             ▼
┌─────────────────────────────────────────┐
│     Prisma (6.19.0)                     │
│     (ORM)                               │
│  ├─ PrismaClient                        │
│  └─ Type-safe queries                   │
└─────────────────────────────────────────┘
             ▲
             │ Se conecta a
             ▼
┌─────────────────────────────────────────┐
│     MySQL (8.x Azure)                   │
│     (Base de datos)                     │
│  └─ dev_commercial                      │
└─────────────────────────────────────────┘
             ▲
             │ Se deployea en
             ▼
┌─────────────────────────────────────────┐
│     Docker                              │
│     (Containerización)                  │
│  └─ docker-registry.alternativasinteligentes.com
└─────────────────────────────────────────┘
```

---

## Configuración del Entorno

### Variables de Entorno Obligatorias

```bash
# Host del microservicio
COMMERCIAL_MICROSERVICE_HOST=localhost

# Puerto TCP
COMMERCIAL_MICROSERVICE_PORT=4005

# Transport type (0 = TCP)
COMMERCIAL_MICROSERVICE_TRANSPORT=0

# Conexión MySQL (Azure)
DATABASE_URL="mysql://user:password@muvinapp-preprod-db.mysql.database.azure.com/dev_commercial"
```

### Validación de Entorno

El archivo `src/config/environments.ts` valida estas variables con Joi:

```typescript
const envVarsSchema = Joi.object({
  HOST: Joi.string().default('localhost'),
  PORT: Joi.number().default(4005),
  DATABASE_URL: Joi.string().required(),
  // ... más variables
});
```

---

## Database Connection

### Driver MySQL

```typescript
// Prisma schema
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

// Formato de DATABASE_URL
mysql://[user[:password]@][host][:port]/[database]

// Ejemplo
mysql://dev_user:SecurePass123@muvinapp-preprod-db.mysql.database.azure.com/dev_commercial
```

### Opciones de Conexión

```typescript
// En PrismaService (OnModuleInit)
await this.$connect();  // Conecta a MySQL

// En app shutdown
await this.$disconnect();  // Desconecta gracefully
```

---

## NestJS Microservices (TCP Transport)

### Configuración del Transport

```typescript
// src/config/transport.ts
export const MicroserviceOptions = {
  transport: Transport.TCP,
  options: {
    host: process.env.HOST,
    port: process.env.PORT,  // 4005
  },
};

// En main.ts
app.connectMicroservice(MicroserviceOptions);
```

### Message Pattern

```typescript
// En controller
@MessagePattern('commercial.contracts.create')
async create(@Payload() payload) {
  return this.contractsService.createContract(payload);
}

// Cliente RPC
const client = new ClientProxy(...);
client.send('commercial.contracts.create', { ... });
```

---

## Prisma Schema Overview

El schema está en `prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

// 5 modelos principales
model contracts { ... }
model entities { ... }
model products { ... }
model roles { ... }
model entity_roles { ... }
```

### Generación de Cliente

```bash
# Generar Prisma Client
npx prisma generate

# Ejecutar migraciones
npx prisma migrate deploy

# Ver BD en Prisma Studio
npx prisma studio
```

---

## Setup Local (Desarrollo)

### Requisitos

- **Node.js 20.x o superior**
- **npm o yarn**
- **MySQL 8.x corriendo localmente** (o credenciales a instancia remota)
- **Git**

### Instalación

```bash
# 1. Clonar repositorio
git clone https://github.com/muvin-com/ms-commercial
cd ms-commercial

# 2. Instalar dependencias
npm install

# 3. Configurar variables de entorno
cp .env.example .env
# Editar .env con tus credenciales MySQL

# 4. Generar Prisma Client
npx prisma generate

# 5. Ejecutar migraciones
npx prisma migrate deploy

# 6. Iniciar en desarrollo
npm run dev
# O con watch mode
npm run start:dev
```

### Scripts NPM Disponibles

```json
{
  "start": "nest start",
  "start:dev": "nest start --watch",
  "start:debug": "nest start --debug --watch",
  "build": "nest build",
  "test": "jest",
  "test:watch": "jest --watch",
  "test:cov": "jest --coverage",
  "lint": "eslint src",
  "format": "prettier --write src"
}
```

---

## Docker

### Dockerfile

Ubicado en `Dockerfile` en la raíz:

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 4005
CMD ["npm", "run", "start"]
```

### Build Docker

```bash
# Buildear imagen
docker build -t ms-commercial:latest .

# Correr contenedor
docker run -p 4005:4005 \
  -e DATABASE_URL="mysql://..." \
  -e COMMERCIAL_MICROSERVICE_HOST="0.0.0.0" \
  ms-commercial:latest

# O con docker-compose
docker-compose up -d
```

---

## CI/CD (GitHub Actions)

### Deploy a Dev

Archivo: `.github/workflows/deploy-dev.yml`

```yaml
name: Deploy Dev
on:
  push:
    branches: [dev]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docker
        run: docker build -t docker-registry.alternativasinteligentes.com/ms-commercial:dev .
      - name: Push to Registry
        run: docker push docker-registry.alternativasinteligentes.com/ms-commercial:dev
      - name: Deploy SSH
        run: |
          ssh user@server 'docker pull ... && docker-compose up -d'
```

### Deploy a Producción

Similar a dev, pero triggered en rama `main`.

---

## Dependencias Externas (Microservicios)

El proyecto depende de:

| Microservicio | Puerto | Función | Contactado Por |
|---|---|---|---|
| **ms-auth** | TCP | Validar API keys, empresas | ContractsService, ValidationService |
| **ms-integrations** | TCP | Enviar emails | ContractsService (notificaciones) |
| **ms-logs** | TCP | Auditoría legacy | ContractsService |

Estas comunicaciones son opcionables/asincrónicas en algunos casos.

---

## Base de Datos (MySQL en Azure)

### Conexión

```
Servidor: muvinapp-preprod-db.mysql.database.azure.com
Base de datos: dev_commercial
Usuario: dev_user
Contraseña: (en variables de entorno)
```

### Acceso a Prisma Studio

```bash
npx prisma studio
# Se abre http://localhost:5555
```

### Tamaño y Performance

| Métrica | Estimado |
|---------|----------|
| Registros en contracts | 10K - 100K |
| Registros en entities | 1K - 10K |
| Registros en products | 100 - 1K |
| Índices | 3-5 por tabla |
| Backups | Automáticos Azure |

---

## Versiones Compatible Mínimas

| Tecnología | Mínimo | Recomendado |
|-----------|--------|------------|
| Node.js | 18.x | 20.x LTS |
| npm | 8.x | 10.x |
| Docker | 20.x | 24.x |
| MySQL | 5.7 | 8.x |

---

## Polyfills y Compatibilidad

- **reflect-metadata**: Necesario para decoradores de NestJS
- **class-validator/transformer**: Validación de DTOs con decoradores
- **Prisma**: Genera tipos automáticamente desde schema

---

## Performance Notes

> [!info]
> **Node 20 LTS**: Versión de larga duración, recomendada para producción.

> [!warning]
> **Prisma Client**: Se genera en tiempo de build. Cambios al schema requieren regenerar.

> [!info]
> **MySQL Connection Pool**: Prisma gestiona automáticamente el pool. Configurable via DATABASE_URL.

---

## Documentos Relacionados

- [[vision-general|Visión General]] - Propósito del proyecto
- [[arquitectura-alto-nivel|Arquitectura]] - Diagramas y flujos
- [[glosario|Glosario]] - Términos técnicos
- [[../07-operacion-y-despliegue/requisitos-entorno|Requisitos de Entorno]] - Setup completo
- [[../07-operacion-y-despliegue/build-y-despliegue|Build y Despliegue]] - CI/CD en detalle


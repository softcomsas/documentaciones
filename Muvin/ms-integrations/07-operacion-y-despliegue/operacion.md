# Operación y Despliegue

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Requisitos del entorno

### Servicios requeridos

| Servicio | Versión | Propósito |
|---|---|---|
| Node.js | ≥18 | Runtime |
| MySQL | 8.0 | Persistencia |
| Redis | ≥6.0 | Cola Bull |

### Variables de entorno

| Variable | Ejemplo | Descripción |
|---|---|---|
| `HOST` | `0.0.0.0` | Host de escucha TCP |
| `PORT` | `4006` | Puerto TCP |
| `DATABASE_URL` | `mysql://user:pass@host:3306/db_integrations` | Conexión MySQL |
| `REDIS_HOST` | `localhost` | Host de Redis |
| `REDIS_PORT` | `6379` | Puerto de Redis |

> [!warning] No existe `.env.example`
> Debe crearse manualmente. Ver [[deuda-tecnica#DT-03]].

---

## Build y despliegue

### Desarrollo local

```bash
# Instalar dependencias
npm install

# Levantar MySQL (solo MySQL, el servicio está comentado en docker-compose)
docker-compose -f docker/docker-compose.yml up -d

# Ejecutar migraciones Prisma
npx prisma migrate deploy

# Arrancar en modo desarrollo
npm run start:dev
```

### Producción

```bash
# Build
npm run build

# Ejecutar migraciones
npx prisma migrate deploy

# Arrancar
npm run start:prod
```

### Migraciones Prisma disponibles

| Migración | Fecha | Descripción |
|---|---|---|
| `20251108205145` | 2025-11-08 | Migración inicial |
| `20251109194039` | 2025-11-09 | Migración 2 |
| `20251109224454` | 2025-11-09 | Migración 3 |

---

## Configuración de Google

Para que el servicio funcione se deben realizar los siguientes pasos **fuera** del código:

1. Crear un service account en Google Cloud Console
2. Habilitar **Domain-Wide Delegation** en Google Workspace Admin
3. Crear un topic de Google Pub/Sub con nombre igual al `project` del service account
4. Configurar el webhook de Pub/Sub apuntando al MS que llama a este microservicio
5. Insertar credenciales en `gmail_credentials` y scopes en `gmail_scopes`

---

## Ver también

- [[arquitectura-alto-nivel]]
- [[stack-tecnologico]]
- [[deuda-tecnica]]

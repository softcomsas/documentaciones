# Requisitos de Entorno

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## Variables de entorno requeridas

| Variable | Descripción | Ejemplo | Obligatoria |
|----------|-------------|---------|-------------|
| `HOST` | Dirección en la que escucha el microservicio TCP | `localhost` o `0.0.0.0` | Sí |
| `PORT` | Puerto TCP del microservicio | `4001` | Sí |
| `DATABASE_URL` | Connection string de MySQL 8.0 | `mysql://user:pass@host:3306/db_name` | Sí |

> [!info] Validación al inicio
> Las variables son validadas con Joi en `src/config/environments.ts` al arrancar. Si alguna falta, el proceso termina con error inmediatamente (fail-fast).

---

## Dependencias de infraestructura

| Servicio | Versión | Propósito | Obligatorio |
|----------|---------|-----------|-------------|
| MySQL | 8.0 | Base de datos principal del microservicio | Sí |
| ms-commercial | — | Microservicio externo (TCP) | Sí (en tiempo de ejecución) |
| ms-logs | — | Microservicio externo (TCP, emit) | No (errores silenciosos si no disponible) |
| ms-integrations | — | Microservicio externo (TCP, emit) | No (errores silenciosos si no disponible) |

---

## Requisitos de Node

| Requisito | Valor |
|-----------|-------|
| Node.js | >= 20 |
| npm | >= 10 |

---

## Setup inicial (primera vez)

```bash
# 1. Copiar template de variables de entorno
cp .env-template .env
# Editar .env con los valores reales

# 2. Instalar dependencias
npm install

# 3. Generar cliente Prisma
npm run prisma:generate

# 4. Ejecutar migraciones (cuando estén disponibles)
npm run prisma:migrate

# 5. Iniciar en modo desarrollo
npm run start:dev
```

> [!warning] `prisma:generate` es obligatorio antes del primer build. Sin este paso, el alias `@db` no resuelve y la compilación falla.

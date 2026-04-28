# Índice de Archivos de Datos

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Migraciones de base de datos

| Archivo | Formato | Propósito | Regenerable | Notas |
|---|---|---|---|---|
| `prisma/migrations/migration_lock.toml` | TOML | Lock de proveedor de BD (`mysql`) | ✅ Auto (Prisma) | No editar manualmente |
| `prisma/migrations/20251108205145/migration.sql` | SQL | Primera migración (tablas iniciales) | ✅ Auto (Prisma) | ⚠️ Pendiente de verificar contenido exacto |
| `prisma/migrations/20251109194039/migration.sql` | SQL | Segunda migración | ✅ Auto (Prisma) | ⚠️ Pendiente de verificar contenido exacto |
| `prisma/migrations/20251109224454/migration.sql` | SQL | Tercera migración | ✅ Auto (Prisma) | ⚠️ Pendiente de verificar contenido exacto |

---

## Esquema de base de datos

| Archivo | Formato | Propósito | Regenerable |
|---|---|---|---|
| `prisma/schema.prisma` | Prisma SDL | Definición de modelos, enums y relaciones MySQL | Manual — fuente de verdad |
| `prisma/generated/client/` | TypeScript + binarios | Cliente Prisma generado | ✅ Auto (`npm run prisma:generate`) |

> [!warning] Cliente generado no se commitea
> La carpeta `prisma/generated/client/` es generada por `prisma generate`. Debe ejecutarse en cada `npm install` o después de cambiar `schema.prisma`. Ver [[build-y-despliegue]].

---

## Archivos de configuración del proyecto

| Archivo | Formato | Propósito | Manual / Auto |
|---|---|---|---|
| `package.json` | JSON | Dependencias y scripts npm | Manual |
| `tsconfig.json` | JSON | Configuración TypeScript base | Manual |
| `tsconfig.build.json` | JSON | TypeScript para build (excluye tests) | Manual |
| `tsconfig.paths.json` | JSON | Path aliases (`@common`, `@config`, etc.) | Manual |
| `nest-cli.json` | JSON | Configuración NestJS CLI | Manual |
| `eslint.config.mjs` | ESM | Reglas ESLint + Prettier | Manual |

---

## Archivos de infraestructura

| Archivo | Formato | Propósito | Manual / Auto |
|---|---|---|---|
| `docker/Dockerfile` | Dockerfile | Build multi-stage para producción | Manual |
| `docker/docker-compose.yml` | YAML | Levanta MySQL en desarrollo | Manual |

---

## Variables de entorno requeridas

> No existe archivo `.env.example` en el repositorio. Las variables requeridas se infieren del código fuente.

| Variable | Tipo | Requerida | Descripción |
|---|---|---|---|
| `HOST` | string | ✅ | Host en que escucha el microservicio TCP |
| `PORT` | number | ✅ | Puerto TCP (documentado como `4006`) |
| `DATABASE_URL` | string | ✅ | URL MySQL (`mysql://user:pass@host:port/db`) |
| `REDIS_HOST` | string | ✅ | Host del servidor Redis |
| `REDIS_PORT` | number | ✅ | Puerto Redis |

> [!danger] Sin .env.example
> No existe archivo de referencia para las variables de entorno. Cualquier desarrollador nuevo debe inferir las variables leyendo `src/config/environments.ts`. Ver [[deuda-tecnica]].

---

## Archivos binarios / generados

| Artefacto | Ubicación | Descripción |
|---|---|---|
| Build compilado | `dist/` (en .gitignore) | Salida de `npm run build` |
| Prisma client binarios | `prisma/generated/` | Binarios nativos por plataforma |

---

## Ver también

- [[requisitos-entorno]]
- [[configuracion]]
- [[build-y-despliegue]]
- [[diagrama-er-global]]

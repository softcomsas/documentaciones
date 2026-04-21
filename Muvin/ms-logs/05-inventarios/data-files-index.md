# Índice de Archivos de Datos — `muvin-ms-logs`

> **Última revisión:** 2026-04-21
> **Incluye:** scripts SQL, configuración, seeds, fixtures, binarios embebidos

---

## Archivos de datos identificados

| Archivo | Ruta | Propósito | Formato | ¿Se regenera? | Estado |
|---------|------|-----------|---------|---------------|--------|
| **Schema Prisma** | `prisma/schema.prisma` | Definición del modelo de datos — fuente de verdad del esquema | DSL Prisma | Manual / `prisma generate` | ✅ Activo |
| **Migration SQL** | `prisma/migrations/20251021181757_/migration.sql` | Crea las 7 tablas y 3 enums del esquema inicial | SQL | Auto-generado por `prisma migrate dev` | ✅ Activo |
| **Migration Lock** | `prisma/migrations/migration_lock.toml` | Lockea el proveedor de BD (`mysql`) | TOML | Auto-generado por Prisma | ✅ Activo |
| **Docker Compose** | `docker/docker-compose.yml` | Levanta MySQL 8.0 para desarrollo local | YAML | Manual | ⚠️ Incompleto (MS comentado) |
| **Dockerfile** | `docker/Dockerfile` | Imagen de producción del MS | Dockerfile | Manual | ✅ Activo |
| **ESLint config** | `eslint.config.mjs` | Reglas de linting | ESM/JS | Manual | ✅ Activo |
| **tsconfig** | `tsconfig.json`, `tsconfig.build.json`, `tsconfig.paths.json` | Configuración TypeScript y path aliases | JSON | Manual | ✅ Activo |
| **nest-cli.json** | `nest-cli.json` | Configuración de NestJS CLI (build, assets) | JSON | Manual | ✅ Activo |
| **package.json** | `package.json` | Dependencias y scripts npm | JSON | Manual / npm | ✅ Activo |

---

## Archivos de configuración de entorno

| Variable | Tipo | Obligatoria | Descripción |
|----------|------|-------------|-------------|
| `LOGS_MICROSERVICE_HOST` | `string` | ✅ Sí | Host donde escucha el MS (ej: `0.0.0.0`) |
| `LOGS_MICROSERVICE_PORT` | `number` | ✅ Sí | Puerto TCP (ej: `3002`) |
| `LOGS_MICROSERVICE_TRANSPORT` | `number` | ✅ Sí | Enum de transporte NestJS (0=TCP, 1=Redis, etc.) |
| `DATABASE_URL` | `string` | ✅ Sí | Connection string MySQL (ej: `mysql://user:pass@host:3306/db`) |

> ⚠️ No existe un archivo `.env.example` en el repositorio. Ver [[requisitos-entorno]].

---

## Seeds / fixtures

| Tipo | Estado | Nota |
|------|--------|------|
| Seeds de BD | ❌ No existen | No hay datos de semilla — las tablas se crean vacías |
| Fixtures de test | ❌ No existen | No hay tests en el proyecto |

---

## Datos generados en runtime (no archivos)

| Dato | Dónde | Formato | Descripción |
|------|-------|---------|-------------|
| Payloads comprimidos | Tablas `legacy_panel`, `legacy_descargas` | `LONGBLOB` (Brotli) | JSON del payload HTTP original comprimido con Brotli Q11 |
| Responses comprimidas | Tablas `legacy_panel`, `legacy_descargas` | `LONGBLOB` (Brotli) | JSON de la respuesta HTTP comprimida |
| Payloads de trazas | Tablas `traces`, `events` | `JSON` nativo MySQL | Almacenados directamente como JSON sin compresión |
| `search_terms` | Tablas `legacy_panel`, `legacy_descargas` | `VARCHAR` (texto plano) | Campos de negocio extraídos para full-text search |

> ℹ️ La elección de comprimir solo los datos legacy (no las trazas GraphQL) probablemente se debe al mayor tamaño promedio de los payloads REST en comparación con los mensajes GraphQL.

---

## Única migración — contenido resumido

La migración `20251021181757_` crea el esquema completo en un único paso:

```
Tablas creadas:
  ✅ traces              — Trazas GraphQL del gateway
  ✅ events              — Eventos de microservicios
  ✅ users               — Mirror de usuarios del sistema
  ✅ legacy_user_actions — Relación N:M usuario ↔ acción
  ✅ legacy_actions      — Catálogo de endpoints legacy
  ✅ legacy_panel        — Log de requests del sistema PANEL
  ✅ legacy_descargas    — Log de requests del sistema DESCARGAS

Enums creados:
  ✅ EOperations (QUERY, MUTATION)
  ✅ EStatus (PENDING, SUCCESS, ERROR, TIMEOUT)
  ✅ EMethods (GET, POST, PUT, DELETE, PATCH)
  ✅ EProject (LEGACY_PANEL, LEGACY_DESCARGAS)
```

> ⚠️ Solo existe una migración. Esto indica que el esquema es inicial y no ha tenido evoluciones registradas, o que las migraciones anteriores no están versionadas en este repositorio.

---

*Ver también: [[diagrama-er-global]] · [[requisitos-entorno]] · [[build-y-despliegue]] · [[stack-tecnologico]]*

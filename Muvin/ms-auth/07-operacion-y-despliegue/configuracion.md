# Configuración

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## Path aliases TypeScript

Configurados en `tsconfig.paths.json`. Usarlos en lugar de rutas relativas largas:

| Alias | Resuelve a | Contenido |
|-------|-----------|-----------|
| `@common` | `src/common/_index` | Tipos, funciones, interfaces compartidas |
| `@config` | `src/config/_index` | Variables de entorno, transport |
| `@core` | `src/core/_index` | PrismaService, CoreModule |
| `@contracts` | `src/contracts/_index` | Contratos tipados de todos los microservicios |
| `@db` | `prisma/generated/client` | Cliente Prisma generado |

> [!warning] `@db` requiere haber ejecutado `npm run prisma:generate` previamente. Si el directorio no existe, el build falla.

---

## Configuración de TypeScript

| Opción | Valor | Impacto |
|--------|-------|---------|
| `target` | ES2023 | Código compilado moderno |
| `module` | nodenext | Soporte nativo de ES Modules en Node |
| `strict` | true | Tipado estricto — null checks, strict functions |
| `strictNullChecks` | true (implícito) | Los tipos deben manejar explícitamente null/undefined |

---

## ESLint (flat config)

Configurado en `eslint.config.mjs`. Usa ESLint 9+ con flat config. Las reglas están orientadas a TypeScript estricto.

**Pre-commit:** Husky ejecuta lint-staged automáticamente en cada `git commit`. Solo se procesa los archivos staged.

---

## Prettier

Configurado en `.prettierrc`. Formateo automático disponible via:
- `npm run format` — formatea todos los archivos
- Hook pre-commit — formatea solo archivos staged

---

## Prisma

| Configuración | Valor |
|---------------|-------|
| Provider | `mysql` |
| Output del cliente | `prisma/generated/client` |
| URL de conexión | Variable de entorno `DATABASE_URL` |

El cliente generado **no debe editarse manualmente**. Regenerar siempre con `npm run prisma:generate` tras cambios al schema.

# Índice de Archivos de Datos

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Ver también:** [[tree-estructura-archivos]], [[configuracion]]

---

## Archivos de configuración

| Ruta | Propósito | Formato | Regenerable | Notas |
|------|-----------|---------|-------------|-------|
| `.env` (no commiteado) | Variables de entorno del proceso | Texto plano | Manual | Debe contener al menos `HOST` y `PORT` para Redis. ⚠️ No está en el repositorio |
| `package.json` | Manifiesto de dependencias npm | JSON | Manual | Versionado. Contiene scripts, deps, lint-staged |
| `tsconfig.json` | Configuración TypeScript principal | JSON | Manual | Target ES2023, moduleResolution nodenext |
| `tsconfig.build.json` | Configuración TypeScript para producción | JSON | Manual | Excluye `node_modules`, `test`, `dist` |
| `tsconfig.paths.json` | Path aliases de TypeScript | JSON | Manual | ⚠️ Alias `@contracts` apunta a directorio inexistente |
| `nest-cli.json` | Configuración del NestJS CLI | JSON | Manual | Define entryFile, sourceRoot, compilerOptions |
| `eslint.config.mjs` | Configuración ESLint v9 (flat config) | ESM | Manual | Integra typescript-eslint + prettier |

---

## Archivos de infraestructura

| Ruta | Propósito | Formato | Regenerable | Notas |
|------|-----------|---------|-------------|-------|
| `docker/Dockerfile` | Imagen Docker multi-stage para producción | Dockerfile | Manual | Builder (node 20 Alpine) + producción (no-root user `nestjs`) |
| `docker/docker-compose.yml` | Composición de servicios para desarrollo local | YAML | Manual | Levanta MySQL 8.0. El servicio `muvin-ms-integrations` está **comentado** |

---

## Datos hardcodeados (equivalentes a seed data)

| Ubicación en código | Datos embebidos | Descripción | Riesgo |
|--------------------|-----------------|-------------|--------|
| `src/modules/email/functions/rt.ts` línea ~105 | `grains = [{ id: 1, name: 'TRIGO PAN' }, { id: 2, name: 'SOJA' }]` | Catálogo de granos aceptados | 🔴 Solo 2 granos. No extensible sin modificar código |
| `src/modules/email/functions/rt.ts` línea ~295 | `cosecha` válida: `'2324' \| '2425' \| '2526' \| '2627' \| '2728'` | Campañas agrícolas aceptadas | 🔴 Hardcodeado hasta 2028. Se romperá |

---

## Scripts SQL / Seeds / Fixtures

| Estado |
|--------|
| 💀 No existen en el proyecto. El worker no gestiona esquema de BD directamente |

---

## Archivos de secretos / credenciales

| Tipo | Forma de provisión | Notas |
|------|--------------------|-------|
| Clave privada cuenta de servicio Google | Dentro del payload del job Bull (campo `auth.key`) | 🔴 La clave privada **viaja en el payload Redis**. Riesgo de seguridad alto. Ver [[security-inventory]] |
| Credenciales Redis | Variables de entorno `HOST` + `PORT` | 🟡 Sin autenticación Redis en configuración actual |
| Credenciales MySQL | Variables de entorno en `docker-compose.yml` | Solo para dev local. El worker no accede a MySQL |

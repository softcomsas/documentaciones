# Índice de archivos de datos

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Archivos de configuración de entorno

| Archivo | Formato | Propósito | ¿Se regenera? | Estado |
|---------|---------|-----------|---------------|--------|
| `.env` (no commiteado) | Texto plano | Variables de entorno en desarrollo local | Manual | ⚠️ No incluido en repo |
| `docker/docker-compose.yml` | YAML | Define el contenedor y sus variables de entorno | Manual | 🟢 En repo |
| `tsconfig.json` | JSON | Config TypeScript principal | Manual | 🟢 En repo |
| `tsconfig.build.json` | JSON | Config TypeScript para build (excluye tests) | Manual | 🟢 En repo |
| `tsconfig.paths.json` | JSON | Path aliases de TypeScript | Manual | 🟢 En repo |
| `nest-cli.json` | JSON | Configuración del CLI de NestJS | Manual | 🟢 En repo |
| `eslint.config.mjs` | JS Module | Configuración de ESLint (flat config v9) | Manual | 🟢 En repo |
| `package.json` | JSON | Dependencias NPM y scripts | Manual | 🟢 En repo |
| `package-lock.json` | JSON | Lock file de dependencias | Auto (npm) | 🟢 En repo |

## Archivos de infraestructura

| Archivo | Formato | Propósito | ¿Se regenera? | Estado |
|---------|---------|-----------|---------------|--------|
| `docker/Dockerfile` | Dockerfile | Build multi-stage Node.js 20 Alpine | Manual | 🟢 En repo |
| `docker/docker-compose.yml` | YAML | Orquestación local con red `muvin-network` | Manual | 🟢 En repo |
| `docker/DOCKER.md` | Markdown | Guía de uso de Docker para este servicio | Manual | 🚧 No verificado |

## Archivos de build generados (no en repo)

| Archivo/Carpeta | Formato | Propósito | ¿Se regenera? |
|-----------------|---------|-----------|---------------|
| `dist/` | JS compilado | Output del build TypeScript | Auto (`npm run build`) |
| `coverage/` | HTML/JSON | Reporte de cobertura de tests | Auto (`npm run test:cov`) |

## Variables de entorno requeridas

> Estas variables deben estar presentes o el servicio falla al arrancar (validación Joi en `src/config/environments.ts`).

| Variable | Tipo | Ejemplo (docker-compose default) | Descripción |
|----------|------|----------------------------------|-------------|
| `LEGACY_MICROSERVICE_HOST` | `string` | `0.0.0.0` | Host donde escucha el microservicio |
| `LEGACY_MICROSERVICE_PORT` | `number` | `4001` | Puerto TCP del microservicio |
| `LEGACY_MICROSERVICE_TRANSPORT` | `number` | `0` (TCP) | Enum `Transport` de NestJS |
| `LEGACY_MICROSERVICE_CMD` | `string` | `CMD-LEGACY` | Identificador del MessagePattern |
| `LEGACY_MICROSERVICE_BASE_URL` | `string` | `https://dev.muvinapp.com/api/backend/web/` | URL base del backend legacy |

> [!danger] Dato sensible
> `LEGACY_MICROSERVICE_BASE_URL` puede contener credenciales embebidas en la URL si el backend legacy requiere autenticación por URL. Verificar que esto no ocurra. Ver [[security-inventory]].

## Archivos de seeds o fixtures

> **No existen.** Este microservicio no tiene base de datos propia ni archivos de datos de inicialización. Toda la persistencia reside en el sistema backend legacy externo.

## Archivos binarios embebidos

> **No existen.** El proyecto no contiene imágenes, binarios, certificados ni recursos estáticos embebidos.

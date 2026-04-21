# Stack Tecnológico

> **Proyecto:** `muvin-ms-worker`
> **Última revisión:** 2026-04-21
> **Ver también:** [[arquitectura-alto-nivel]], [[core-vs-custom-dependencies]]

---

## Dependencias de producción

| Tecnología | Paquete | Versión declarada | Propósito | Estado vendor | Riesgo |
|-----------|---------|------------------|-----------|--------------|--------|
| NestJS Core | `@nestjs/common`, `@nestjs/core` | `^11.1.9` | Framework base (DI, módulos, decoradores) | ✅ Vigente | 🟢 Bajo |
| NestJS Bull | `@nestjs/bull` | `^11.0.4` | Integración Bull ↔ NestJS (@Process, @Processor) | ✅ Vigente | 🟢 Bajo |
| NestJS Express | `@nestjs/platform-express` | `^11.1.9` | Adaptador HTTP de Express | ⚠️ **No necesario** en un worker sin HTTP | 🟡 Dependencia innecesaria |
| Bull | `bull` | `^4.16.5` | Cola de tareas sobre Redis | ✅ Vigente (mantenimiento activo) | 🟡 El sucesor es BullMQ |
| dotenv | `dotenv` | `^17.2.3` | Carga de variables de entorno desde `.env` | ✅ Vigente | 🟢 Bajo |
| googleapis | `googleapis` | `^166.0.0` | SDK oficial de Google APIs (Gmail, Auth JWT) | ✅ Vigente | 🟢 Bajo |
| joi | `joi` | `^18.0.2` | Validación de esquemas (configuración de entorno) | ✅ Vigente | 🟢 Bajo |
| pdf-parse | `pdf-parse` | `^2.4.5` | Extracción de texto plano desde PDF binario | ⚠️ Mantenimiento mínimo | 🟡 Medio — poca actividad en el repo |
| reflect-metadata | `reflect-metadata` | `^0.2.2` | Polyfill para decoradores TypeScript (requerido por NestJS) | ✅ Vigente | 🟢 Bajo |
| rxjs | `rxjs` | `^7.8.2` | Programación reactiva (importado por NestJS internamente) | ✅ Vigente | 🟢 Bajo |

---

## Dependencias de desarrollo

| Tecnología | Paquete | Versión declarada | Propósito | Estado |
|-----------|---------|------------------|-----------|--------|
| TypeScript | `typescript` | `^5.9.3` | Lenguaje de tipado estático | ✅ Vigente |
| NestJS CLI | `@nestjs/cli` | `^11.0.13` | Herramientas de build (`nest build`) | ✅ Vigente |
| ESLint | `eslint` | `^9.39.1` | Linting (flat config v9) | ✅ Vigente |
| Prettier | `prettier` | `^3.7.0` | Formateo de código | ✅ Vigente |
| Husky | `husky` | `^9.1.7` | Git hooks pre-commit | ✅ Vigente |
| lint-staged | `lint-staged` | `^16.2.7` | Ejecuta linting solo sobre archivos staged | ✅ Vigente |
| typescript-eslint | `typescript-eslint` | `^8.48.0` | Plugin ESLint para TypeScript | ✅ Vigente |
| ts-node | `ts-node` | `^10.9.2` | Ejecución TypeScript en desarrollo | ✅ Vigente |
| tsconfig-paths | `tsconfig-paths` | `^4.2.0` | Resolución de path aliases en runtime dev | ✅ Vigente |
| typescript-transform-paths | `typescript-transform-paths` | `^3.5.5` | Transforma path aliases al compilar | ✅ Vigente |

---

## Infraestructura

| Componente | Tecnología | Versión | Notas |
|-----------|-----------|---------|-------|
| Runtime | Node.js | 20 (Alpine) | Imagen base del Dockerfile |
| Cola de tareas | Redis | No especificada en docker-compose | ⚠️ Pendiente de verificar — no hay servicio Redis en `docker-compose.yml` |
| Base de datos | MySQL | 8.0 | Solo en `docker-compose.yml` local; el worker **no accede directamente a BD** |
| Contenedor | Docker | Multi-stage build | Builder (node 20) → Production (node 20 Alpine) |
| Orquestación | Docker Compose | v3.8 | Uso local; el servicio principal está comentado |

---

## Lenguajes y formatos

| Elemento | Tecnología | Versión target |
|---------|-----------|---------------|
| Lenguaje principal | TypeScript | Target: `ES2023` |
| Resolución de módulos | `nodenext` | Compatible con Node 20 ESM/CJS |
| Decoradores | `experimentalDecorators: true` + `emitDecoratorMetadata: true` | Requerido por NestJS |

---

## Autenticación externa (Gmail)

| Mecanismo | Descripción | Riesgo |
|-----------|------------|--------|
| OAuth 2.0 JWT (Service Account) | La clave privada (`key`) se pasa dentro del payload del job en Redis | 🔴 **Alto** — la clave privada viaja por Redis sin cifrado |
| `googleapis` v166 | Usa `google.auth.JWT` con `email`, `key`, `scopes`, `subject` | 🟢 SDK oficial y actualizado |
| Subject (Domain-wide delegation) | El campo `subject` impersona `${local}@${domain}` | 🟡 Requiere que la cuenta de servicio tenga DWD habilitado en Google Workspace |

---

## Decisiones de diseño notables

| Decisión | Descripción | Alternativa moderna |
|---------|------------|-------------------|
| Bull (v4) en lugar de BullMQ | Se usa la versión anterior de Bull | BullMQ tiene mejor rendimiento, tipos nativos TS y mantenimiento activo |
| `pdf-parse` | Librería con bajo mantenimiento | `pdfjs-dist` (Mozilla) o `unpdf` |
| Sin servidor HTTP | El worker no expone ningún puerto; `@nestjs/platform-express` está incluido pero no se usa | Remover la dependencia |
| Granos hardcodeados | Solo TRIGO PAN y SOJA en el array `grains` de `rt.ts` | Cargar desde BD o configuración |
| Cosechas hardcodeadas | Solo 5 valores: 2324, 2425, 2526, 2627, 2728 | 🔴 Se romperá en 2028/2029 |

> [!warning] Deuda técnica
> El alias `@contracts` está declarado en `tsconfig.paths.json` pero el directorio `src/contracts/` no existe. Cualquier import usando ese alias fallará en build.
> Ver [[deuda-tecnica]].

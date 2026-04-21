# Stack tecnológico

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Tabla de dependencias de producción

| Tecnología | Versión | Propósito | Soporte vendor | Riesgo |
|------------|---------|-----------|----------------|--------|
| **Node.js** | 20 (Alpine) | Runtime de ejecución | ✅ LTS activo (hasta 2026-04) | 🟡 Próxima EOL |
| **NestJS** | 11.x | Framework del microservicio | ✅ Vigente | 🟢 |
| **TypeScript** | 5.7.x | Lenguaje de implementación | ✅ Vigente | 🟢 |
| **@nestjs/microservices** | 11.1.6 | Transporte TCP entre microservicios | ✅ Vigente | 🟢 |
| **@nestjs/axios** | 4.0.1 | Cliente HTTP wrapping Axios | ✅ Vigente | 🟢 |
| **axios** | 1.12.x | HTTP client hacia el backend legacy | ✅ Vigente | 🟢 |
| **RxJS** | 7.8.x | Manejo reactivo de observables | ✅ Vigente | 🟢 |
| **class-validator** | 0.14.x | Validación de DTOs | ✅ Vigente | 🟢 |
| **class-transformer** | 0.5.x | Transformación de objetos | ✅ Vigente | 🟢 |
| **joi** | 18.x | Validación del schema de variables de entorno | ✅ Vigente | 🟢 |
| **dotenv** | 17.x | Carga de `.env` | ✅ Vigente | 🟢 |
| **reflect-metadata** | 0.2.x | Decoradores TypeScript (requerido por NestJS) | ✅ Vigente | 🟢 |

## Dependencias de desarrollo

| Tecnología | Versión | Propósito | Riesgo |
|------------|---------|-----------|--------|
| **Jest** | 30.x | Testing unitario | 🟢 |
| **ts-jest** | 29.x | Integración TypeScript con Jest | 🟢 |
| **Supertest** | 7.x | Testing HTTP | 🟢 |
| **ESLint** | 9.x (flat config) | Linting | 🟢 |
| **Prettier** | 3.4.x | Formateo de código | 🟢 |
| **Husky** | 9.x | Git hooks | 🟢 |
| **lint-staged** | 16.x | Linting previo al commit | 🟢 |
| **typescript-transform-paths** | 3.5.x | Resolución de path aliases en build | 🟢 |
| **tsconfig-paths** | 4.2.x | Resolución de path aliases en runtime/test | 🟢 |

## Infraestructura y despliegue

| Tecnología | Versión | Propósito | Riesgo |
|------------|---------|-----------|--------|
| **Docker** | multi-stage | Contenedorización | 🟢 |
| **Docker Compose** | 3.8 | Orquestación local | 🟢 |
| **Alpine Linux** | (base node:20-alpine) | OS del contenedor (imagen liviana) | 🟢 |

## Protocolo de transporte

| Aspecto | Valor |
|---------|-------|
| Tipo | TCP (NestJS Microservice Transport) |
| Valor enum `Transport` | `0` (`Transport.TCP`) |
| Puerto por defecto | `4001` |
| Host por defecto | `0.0.0.0` |
| Pattern de mensajes | `{ cmd: LEGACY_MICROSERVICE_CMD }` |

## Arquitectura de TypeScript

| Configuración | Valor | Implicancia |
|---------------|-------|-------------|
| `target` | ES2023 | Código moderno sin transpilación de features modernos |
| `module` | nodenext | Módulos ES nativos para Node |
| `moduleResolution` | nodenext | Resolución moderna de módulos |
| `strictNullChecks` | `true` | Seguridad de nulos habilitada |
| `noImplicitAny` | `false` | ⚠️ Se permiten `any` implícitos — reduce seguridad de tipos |
| `experimentalDecorators` | `true` | Requerido por NestJS |
| `emitDecoratorMetadata` | `true` | Requerido por NestJS DI |

## Path aliases configurados

| Alias | Resuelve a | Propósito |
|-------|-----------|-----------|
| `@common` | `src/common/_index` | Utilidades compartidas |
| `@config` | `src/config/_index` | Variables de entorno |
| `@contract-ms-legacy` | `src/contracts/ms-legacy/_index.ts` | Contrato público del microservicio |
| `@types` | `src/types/_index.ts` | Sistema de tipos internos |

## Backend externo (sistema legacy)

| Aspecto | Valor |
|---------|-------|
| URL base (dev) | `https://dev.muvinapp.com/api/backend/web/` |
| Protocolo | HTTPS / REST |
| Timeout HTTP | 5000 ms |
| Max redirects | 5 |
| Autenticación | ⚠️ Pendiente de verificar (no visible en código del microservicio) |

> [!warning] Riesgo de versión
> Node.js 20 entra en EOL en **abril 2026**. Se recomienda migrar a Node.js 22 LTS en el próximo ciclo de mantenimiento.

> [!info] Nota sobre `noImplicitAny: false`
> Esta configuración permite el uso de `any` implícito, lo que reduce la seguridad del tipado estático. En el código actual ya hay usos explícitos de `as unknown as T` en `service.ts` para forzar castings. Si bien es aceptable para un proxy simple, es una deuda técnica a resolver.

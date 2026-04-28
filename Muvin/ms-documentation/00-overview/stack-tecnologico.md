---
tags: [stack, tecnología, versiones]
última-revisión: 2026-04-27
---

# Stack tecnológico

> **Proyecto:** `muvin-ms-cpe`

## Runtime y framework

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|------------|---------|-----------|---------------|--------|
| Node.js | 20 (LTS) | Runtime de ejecución | Vigente (LTS activo) | 🟢 |
| NestJS | ^11.1.9 | Framework principal (microservicio TCP) | Vigente | 🟢 |
| TypeScript | ^5.9.3 | Lenguaje | Vigente | 🟢 |
| RxJS | ^7.8.2 | Manejo de streams / observables | Vigente | 🟢 |

## Base de datos

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|------------|---------|-----------|---------------|--------|
| MySQL | 8.0 | Base de datos relacional | Vigente | 🟢 |
| Prisma ORM | ^6.19.0 | ORM / cliente de base de datos | Vigente | 🟢 |

## Validación y configuración

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|------------|---------|-----------|---------------|--------|
| Joi | ^18.0.2 | Validación de variables de entorno al arranque | Vigente | 🟢 |
| dotenv | ^17.2.3 | Carga de variables de entorno | Vigente | 🟢 |
| reflect-metadata | ^0.2.2 | Soporte decoradores TypeScript / NestJS DI | Vigente | 🟢 |

## Infraestructura y despliegue

| Tecnología | Versión / Detalle | Propósito | Estado | Riesgo |
|------------|-------------------|-----------|--------|--------|
| Docker | node:20-alpine | Containerización (multi-stage build) | Vigente | 🟢 |
| HashiCorp Vault | `vault.alternativasinteligentes.com` | Gestión de secretos en CI/CD | 🚧 Pendiente de verificar versión | 🟡 |
| GitHub Actions | — | CI/CD | Vigente | 🟢 |
| Docker Registry privado | `docker-registry.alternativasinteligentes.com` | Almacenamiento de imágenes | 🚧 Pendiente de verificar | 🟡 |
| SSH | — | Deploy al servidor DEV | Vigente | 🟢 |

## Herramientas de desarrollo

| Tecnología | Versión | Propósito |
|------------|---------|-----------|
| ESLint | ^9.39.2 | Linting |
| Prettier | ^3.7.4 | Formateo de código |
| Husky | ^9.1.7 | Git hooks |
| lint-staged | ^16.2.7 | Lint solo de archivos staged |
| ts-node | ^10.9.2 | Ejecución TypeScript en desarrollo |
| tsconfig-paths | ^4.2.0 | Resolución de path aliases en runtime |
| typescript-transform-paths | ^3.5.5 | Transformación de path aliases en build |

## Protocolo de comunicación

| Protocolo | Detalle | Propósito |
|-----------|---------|-----------|
| TCP (NestJS Microservices) | `Transport.TCP`, puerto `4002` | Comunicación inter-microservicios |

> [!info] Sin HTTP
> Este microservicio **no expone endpoints HTTP**. Toda comunicación es vía mensajes TCP usando el sistema de microservicios de NestJS (`send` / `emit`).

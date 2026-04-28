# Stack Tecnológico

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Dependencias de producción

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|---|---|---|---|---|
| **Node.js** | 20.x (imagen Docker) | Runtime JavaScript | ✅ LTS activo | 🟢 |
| **TypeScript** | ^5.9.3 | Lenguaje principal | ✅ Vigente | 🟢 |
| **NestJS Core** | ^11.1.9 | Framework de aplicación | ✅ Vigente | 🟢 |
| **NestJS Microservices** | ^11.1.9 | Transporte TCP para mensajes | ✅ Vigente | 🟢 |
| **NestJS Platform-Express** | ^11.1.9 | Adaptador HTTP (usado solo para bootstrap) | ✅ Vigente | 🟡 Sin HTTP real en este microservicio |
| **@nestjs/bull** | ^11.0.4 | Integración Bull + NestJS | ✅ Vigente | 🟢 |
| **Bull** | ^4.16.5 | Sistema de colas basado en Redis | ✅ Vigente | 🟡 Bull v4 en mantenimiento; BullMQ es el sucesor |
| **Prisma Client** | ^6.19.0 | ORM generado para MySQL | ✅ Vigente | 🟢 |
| **googleapis** | ^166.0.0 | SDK oficial de Google APIs (Gmail) | ✅ Vigente | 🟢 |
| **class-validator** | ^0.14.2 | Validación de DTOs con decoradores | ✅ Vigente | 🟢 |
| **class-transformer** | ^0.5.1 | Serialización/deserialización de objetos | ✅ Vigente | 🟢 |
| **joi** | ^18.0.1 | Validación de esquema de variables de entorno | ✅ Vigente | 🟢 |
| **dotenv** | ^17.2.3 | Carga de `.env` | ✅ Vigente | 🟢 |
| **rxjs** | ^7.8.2 | Programación reactiva (requerida por NestJS) | ✅ Vigente | 🟢 |
| **reflect-metadata** | ^0.2.2 | Soporte de decoradores TypeScript | ✅ Vigente | 🟡 Dependencia implícita de decoradores |

---

## Dependencias de infraestructura (runtime)

| Tecnología | Versión | Propósito | Estado | Riesgo |
|---|---|---|---|---|
| **MySQL** | 8.0 (Docker) | Base de datos relacional | ✅ Vigente | 🟢 |
| **Redis** | No especificado | Backend de Bull (colas) | ✅ Vigente | 🟡 Versión no pinada en docker-compose |
| **Docker / Docker Compose** | v3.8 | Contenedores de desarrollo | ✅ Vigente | 🟢 |

> [!warning] Redis sin versión fija
> El `docker-compose.yml` solo levanta MySQL. Redis se asume disponible externamente. Su versión no está documentada ni pinada. Ver [[requisitos-entorno]].

---

## Dependencias de desarrollo

| Tecnología | Versión | Propósito |
|---|---|---|
| **@nestjs/cli** | ^11.0.12 | Build y scaffolding |
| **Prisma CLI** | ^6.19.0 | Generación de cliente y migraciones |
| **ESLint** | ^9.39.1 + typescript-eslint | Linting |
| **Prettier** | ^3.6.2 | Formato de código |
| **Husky** | ^9.1.7 | Git hooks |
| **lint-staged** | ^16.2.6 | Lint solo en archivos staged |
| **ts-node** | ^10.9.2 | Ejecución TypeScript en desarrollo |
| **tsconfig-paths** | ^4.2.0 | Resolución de path aliases en runtime |
| **typescript-transform-paths** | ^3.5.5 | Resolución de path aliases en build |

---

## Protocolo de transporte

| Parámetro | Valor |
|---|---|
| Tipo | `Transport.TCP` (valor `0`) |
| Host | Variable de entorno `HOST` |
| Puerto | Variable de entorno `PORT` (documentado como `4006`) |
| Serialización | JSON (por defecto NestJS TCP) |

---

## Protocolo de colas

| Parámetro | Valor |
|---|---|
| Cola | `email` |
| Job | `email.notification` |
| Backend | Redis |
| Librería | Bull v4 |
| Consumer | ⚠️ **No declarado en este microservicio** — procesado por otro servicio externo |

> [!danger] Sin consumer de cola en este servicio
> `QueueService.add()` encola jobs en Bull pero **no existe ningún `@Processor`** en este repositorio. El worker que procesa los jobs `email.notification` vive en otro microservicio. Esto no está documentado ni existe referencia cruzada. Ver [[deuda-tecnica]].

---

## Tecnologías definidas en contratos pero NO implementadas en este servicio

| Contrato | Descripción | Estado |
|---|---|---|
| `TContractLogs` / `MsLogs` | Contratos hacia microservicio de logs | 💀 Solo definición de tipos, sin uso |
| `TContractCommercial` / `MsCommercial` | Contratos hacia microservicio comercial | 💀 Solo definición de tipos, sin uso |

---

## Ver también

- [[arquitectura-alto-nivel]]
- [[requisitos-entorno]]
- [[core-vs-custom-dependencies]]
- [[deuda-tecnica]]

# Core vs Custom Dependencies

**Última actualización:** 2026-04-27

## Dependencias de Producción

### Core (@nestjs/*)

- @nestjs/common
- @nestjs/core
- @nestjs/microservices
- reflect-metadata

### Data Layer

- @prisma/client (ORM)

### Validation

- class-validator
- class-transformer
- joi (env vars)

### Total

~8 dependencias de producción

---

## Dependencias de Desarrollo

### Testing

- @nestjs/testing
- jest
- ts-jest

### Linting & Format

- eslint
- @typescript-eslint
- prettier

### Tipos

- @types/node
- @types/jest
- typescript

### Total

~12+ dependencias de desarrollo

---

## Dependencias Externas (RPC)

El servicio se comunica con:

- **ms-auth** (validación)
- **ms-integrations** (notificaciones)
- **ms-logs** (auditoría)

---

## Documentos Relacionados

- [[../01-modulos/_indice-modulos|Módulos]] - Stack completo
- [[../00-overview/stack-tecnologico|Stack Tecnológico]] - Versiones


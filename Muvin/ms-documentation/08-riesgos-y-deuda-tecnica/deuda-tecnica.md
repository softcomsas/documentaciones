---
tags: [deuda técnica, riesgos, prioridades]
última-revisión: 2026-04-27
---

# Deuda técnica

## Inventario priorizado

| # | Descripción | Impacto | Esfuerzo | Severidad |
|---|-------------|:-------:|:--------:|:---------:|
| 1 | Esquema Prisma vacío — no hay modelos de datos | Alto | Bajo | 🔴 |
| 2 | Repositorios no implementados (`src/core/repositories/`) | Alto | Bajo–Medio | 🔴 |
| 3 | Sin handlers `@MessagePattern` — el MS no responde a ningún mensaje | Alto | Alto | 🔴 |
| 4 | Sin tests — cobertura 0% | Medio | Medio | 🟡 |
| 5 | `docker-compose.yml` tiene el servicio `muvin-ms-cpe` comentado | Bajo | Muy bajo | 🟡 |
| 6 | `@nestjs/platform-express` incluido pero no se usa HTTP | Muy bajo | Bajo | 🟢 |

## Detalle por ítem

### 1. Esquema Prisma vacío

**Archivo:** `prisma/schema.prisma`
**Estado:** El datasource y el generador están configurados correctamente pero no hay ningún modelo.

`PrismaService` existe e instancia `PrismaClient`, pero no puede acceder a ninguna tabla porque no hay modelos definidos.

**Acción:** Definir los modelos de dominio y ejecutar `npm run prisma:migrate`.

### 2. Repositorios no implementados

**Archivo:** `src/core/repositories/_index.ts`
**Estado:** Vacío. El array `REPOSITORIES` en `CoreModule` está preparado pero sin contenido.

Sin repositorios, los futuros servicios de dominio no tienen capa de abstracción de datos — accederían directamente a `PrismaService`, lo que dificulta el testing y la separación de responsabilidades.

**Acción:** Implementar un repositorio por entidad Prisma. Registrarlos en el array `REPOSITORIES` del `CoreModule`.

### 3. Sin message pattern handlers

**Estado:** No hay ningún `@MessagePattern` registrado en ningún controller.

El microservicio arranca y escucha conexiones TCP pero no procesa ningún mensaje. Desde el punto de vista del ecosistema Muvin, este MS actualmente no tiene utilidad funcional.

**Acción:** Crear módulos de dominio con controllers NestJS decorados con `@MessagePattern(CMDS.xxx)`.

### 4. Sin tests

**Estado:** No hay archivos de test (`*.spec.ts`, `*.test.ts`).

**Acción:** Implementar tests unitarios para servicios y funciones utilitarias. Alcanzar al menos 80% de cobertura.

### 5. docker-compose sin servicio app

**Archivo:** `docker/docker-compose.yml`
**Estado:** El servicio `muvin-ms-cpe` está comentado. Solo levanta MySQL.

**Acción:** Descomentar o agregar el servicio con la configuración de entorno local para facilitar el desarrollo integrado.

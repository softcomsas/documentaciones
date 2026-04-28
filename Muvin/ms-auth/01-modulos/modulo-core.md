# Módulo: Core

> **Ruta/Namespace:** `src/core/`
> **Criticidad:** 🟡 Media — infraestructura base de la aplicación
> **Estado:** ✅ Implementado (parcialmente)

---

## Propósito

Provee los servicios de infraestructura compartidos por toda la aplicación. Es el único módulo con implementación real actualmente. Se declara como `@Global()` en NestJS, lo que significa que sus exports están disponibles en cualquier módulo sin necesidad de importarlo explícitamente.

---

## Componentes

| Componente | Archivo | Estado | Descripción |
|------------|---------|--------|-------------|
| `CoreModule` | `src/core/module.ts` | ✅ | Módulo global que provee PrismaService |
| `PrismaService` | `src/core/services/prisma.ts` | ✅ | Extiende PrismaClient, se conecta al init |
| Repositories | `src/core/repositories/_index.ts` | 💀 Vacío | Estructura reservada — sin implementación |

---

## PrismaService

```typescript
// src/core/services/prisma.ts
@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {
  async onModuleInit() {
    await this.$connect();
  }
}
```

- Extiende `PrismaClient` de `prisma/generated/client`
- Se conecta a MySQL al iniciar el módulo (`onModuleInit`)
- Es inyectable en cualquier servicio o handler de la aplicación

---

## Dependencias

- **Depende de:** `@db` alias → `prisma/generated/client`
- **Es usado por:** (cuando se implementen) todos los handlers que accedan a la BD

---

## Riesgos

- 💀 `src/core/repositories/_index.ts` está vacío — el patrón Repository no está implementado. Los handlers futuros podrían acceder a Prisma directamente, violando la separación de capas.
- ⚠️ Si `prisma generate` no se ejecuta antes del build, el import de `@db` falla.

---

## Archivos fuente relevantes

- `src/core/module.ts`
- `src/core/services/prisma.ts`
- `src/core/services/_index.ts`
- `src/core/repositories/_index.ts`
- `src/core/_index.ts`

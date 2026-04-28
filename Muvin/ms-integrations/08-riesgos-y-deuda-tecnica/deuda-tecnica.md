# Deuda Técnica

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

---

## Resumen

| ID | Categoría | Descripción | Severidad | Esfuerzo |
|---|---|---|---|---|
| DT-01 | Seguridad | Clave privada en payload Bull | 🔴 CRÍTICO | Bajo |
| DT-02 | Tests | Cero cobertura de tests | 🔴 CRÍTICO | Alto |
| DT-03 | Infraestructura | No existe `.env.example` | 🟡 MEDIO | Bajo |
| DT-04 | Infraestructura | Servicio comentado en docker-compose | 🟡 MEDIO | Bajo |
| DT-05 | Código | `console.error` con TODO en environments.ts | 🟡 MEDIO | Bajo |
| DT-06 | Diseño | Credencial única — sin multi-tenant | 🟡 MEDIO | Alto |
| DT-07 | Operación | Watch expira sin renovación automática | 🟡 MEDIO | Medio |
| DT-08 | Código | `_syncLabels()` comentada | 🟡 MEDIO | Bajo |
| DT-09 | Código | `@repositories` alias inválido en tsconfig.paths.json | 🟡 MEDIO | Bajo |
| DT-10 | Código | Repositories declarados pero nunca inyectados | 🟢 BAJO | Bajo |
| DT-11 | Dependencias | Bull v4 → debería migrarse a BullMQ | 🟢 BAJO | Medio |
| DT-12 | Código | `notification()` retorna void sin error propagation | 🔴 CRÍTICO | Bajo |

---

## Detalle

### DT-01 — Clave privada en payload Bull
**Archivo:** `src/modules/gmail/service.ts`
El job encolado incluye `auth.key` (clave privada PEM). Ver [[security-inventory#SEC-01]].
**Fix:** Eliminar `auth` del payload. El worker debe obtener credenciales desde DB o secret manager.

---

### DT-02 — Cero cobertura de tests
No existe ningún archivo de test en el repositorio. Sin tests unitarios, de integración ni E2E.
**Riesgo:** Cualquier refactor puede romper silenciosamente el flujo principal.
**Fix:** Implementar suite con Jest (ya disponible como devDependency). Priorizar:
1. `GmailService.notification()` — lógica más crítica
2. `GmailService._setToken()` — credenciales
3. Repositorios Prisma (mocking)

---

### DT-03 — No existe `.env.example`
No hay plantilla de variables de entorno. Los developers deben inferirlas desde `src/config/environments.ts`.
**Fix:** Crear `.env.example` con:
```
HOST=0.0.0.0
PORT=4006
DATABASE_URL=mysql://user:pass@localhost:3306/db_integrations
REDIS_HOST=localhost
REDIS_PORT=6379
```

---

### DT-04 — Servicio comentado en docker-compose.yml
`docker/docker-compose.yml` solo levanta MySQL. El servicio `muvin-ms-integrations` está completamente comentado.
**Fix:** Descomentar y configurar el servicio, o documentar explícitamente que el arranque local es sin Docker.

---

### DT-05 — `console.error` con TODO en environments.ts
**Archivo:** `src/config/environments.ts`
Hay un `console.error` marcado `// TODO: Remover` que puede imprimir variables de entorno sensibles.
**Fix:** Eliminar la línea.

---

### DT-06 — Credencial única — sin multi-tenant
`findFirstDomain()` toma siempre la primera credencial sin criterio. No escala a múltiples dominios.
**Fix:** Añadir un campo de selección (ej: por `domain`) y soportar múltiples credenciales activas.

---

### DT-07 — Watch expira sin renovación automática
Los watches de Gmail expiran ~7 días. No hay cron ni scheduler para renovarlos.
**Fix:** Implementar un `@Cron` (con `@nestjs/schedule`) que llame `_syncWatches()` cada 6 días.

---

### DT-08 — `_syncLabels()` comentada
**Archivo:** `src/modules/gmail/service.ts`
El método existe pero está comentado en `_init()`. Los labels en la cache in-memory son solo los de DB, no los de Gmail API.
**Impacto:** Si Gmail tiene labels que no están en DB, se pierden mensajes.
**Fix:** Descomentar, implementar o documentar explícitamente la decisión de no sincronizar.

---

### DT-09 — `@repositories` alias inválido
**Archivo:** `tsconfig.paths.json`
El alias `@repositories` apunta a `./src/repositories/_index.ts` que no existe.
**Fix:** Corregir o eliminar el alias.

---

### DT-10 — Repositories declarados pero nunca inyectados
`GmailCredentialScopesRepository` y `GmailScopesRepository` están en el barrel de `core/repositories` y exportados desde `CoreModule`, pero ningún servicio los inyecta.
**Fix:** Eliminarlos del barrel y de CoreModule, o implementar su uso.

---

### DT-11 — Bull v4 → BullMQ
`@nestjs/bull` v11 es un wrapper sobre Bull v4, que no recibe actualizaciones de seguridad activas. BullMQ es el sucesor.
**Fix:** Migrar a `bullmq` + `@nestjs/bullmq` en una ventana de mantenimiento.

---

### DT-12 — `notification()` retorna void sin propagación de errores
**Archivo:** `src/modules/gmail/controller.ts`
El handler usa `@MessagePattern` (request-reply) pero retorna `void`. El caller TCP nunca sabe si ocurrió un error.
**Fix:** Retornar un resultado estructurado (`{success: boolean, error?: string}`) o cambiar a `@EventPattern` si es fuego-y-olvido intencional.

---

## Ver también

- [[security-inventory]]
- [[hotspots]]
- [[recomendaciones-modernizacion]]

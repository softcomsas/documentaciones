# Deuda Técnica

> **Contexto:** [[hotspots]] · [[recomendaciones-modernizacion]]

## Registro de deuda

| # | Ítem | Severidad | Impacto | Esfuerzo | Prioridad |
|---|------|-----------|---------|----------|-----------|
| 1 | [Bug: event.update CMD](#bug-event-update) | 🔴 Crítico | Todos los eventos sin respuesta/duración | Muy bajo (1 línea) | **Ahora** |
| 2 | [Zero test coverage](#zero-tests) | 🔴 Alto | Sin red de seguridad ante cambios | Alto | **Urgente** |
| 3 | [Errores silenciados](#silent-errors) | 🟡 Medio | Pérdida de datos sin detección | Medio | Alta |
| 4 | [PrismaService dual](#dual-prisma) | 🟡 Medio | Doble pool de conexiones | Bajo | Media |
| 5 | [Duration en segundos](#duration-unit) | 🟡 Medio | Métricas de latencia incorrectas en legacy | Bajo | Media |
| 6 | [Void en controllers](#void-controllers) | 🟡 Medio | Errores en write handlers son invisibles | Bajo | Media |
| 7 | [MS comentado en docker-compose](#docker-commented) | 🟡 Bajo | No se puede levantar el stack completo con Docker | Bajo | Baja |
| 8 | [Dead code: IDENTITY](#dead-identity) | 💀 Bajo | Confusión y ruido en el codebase | Muy bajo | Baja |
| 9 | [Dep innecesaria: platform-express](#platform-express) | 💀 Bajo | Dependencia sin uso | Muy bajo | Baja |

---

## Detalle por ítem

### Bug: event.update CMD {#bug-event-update}

**Archivo:** `src/common/cmd/constant.ts`, línea ~17
**Estado actual:**
```typescript
event: {
  create: 'logs.event.create',
  update: 'logs.event.create',  // ← BUG: debería ser 'logs.event.update'
}
```
**Fix:**
```typescript
event: {
  create: 'logs.event.create',
  update: 'logs.event.update',  // ← Fix
}
```
**Impacto del fix:** A partir del fix, los mensajes `logs.event.update` se rutearán al handler correcto y los eventos comenzarán a cerrarse. Los registros históricos con `response/duration = null` NO se pueden retroalimentar automáticamente.

---

### Zero test coverage {#zero-tests}

No existen archivos de test en el proyecto. La configuración de Jest está presente en `package.json` pero sin ningún test.

**Riesgos:**
- Cualquier cambio puede romper funcionalidad existente sin ser detectado
- El bug de `constant.ts` habría sido detectado con un test trivial

**Tests mínimos requeridos:**
1. Test unitario: `constant.ts` — verificar que todos los CMD son strings únicos
2. Test unitario: `terms.ts` — `extractSearchableFieldsFn` y `mergeSearchTermsFn`
3. Test unitario: `json.ts` — compress/decompress round-trip
4. Test de integración: `LegacyService.create()` con mock de Prisma
5. Test de integración: `LegacyService.update()` — verificar unidad de duration

---

### Errores silenciados {#silent-errors}

**Patrón actual en todos los servicios:**
```typescript
} catch (error) {
  console.log(error);
  return null; // o void
}
```

**Impacto:** Un fallo de BD durante `legacy.create` resulta en el log siendo descartado silenciosamente. El caller recibe `void` y no sabe que el log no fue grabado.

**Recomendación:** Usar el `Logger` de NestJS con nivel `error`, y considerar un bus de eventos para errores críticos o integrar con un sistema de alertas (Sentry, Datadog, etc.).

---

### PrismaService dual {#dual-prisma}

**Problema:** `PrismaService` se declara en los `providers` de `MicroservicesModule` Y `LegacyModule`. NestJS crea una instancia por módulo → dos `PrismaClient` activos.

**Fix:**
```typescript
// Crear DatabaseModule global
@Global()
@Module({
  providers: [PrismaService],
  exports: [PrismaService],
})
export class DatabaseModule {}

// Importar solo en AppModule
@Module({ imports: [DatabaseModule, MicroservicesModule, LegacyModule] })
export class AppModule {}
```

---

### Duration en segundos en legacy {#duration-unit}

**Archivo:** `src/modules/legacy/service.ts` — método `update()`
```typescript
// Actual (segundos):
duration: Math.floor((Date.now() - record.createdAt.getTime()) / 1000)

// Correcto (ms — consistente con MicroservicesService):
duration: Date.now() - record.createdAt.getTime()
```

**Impacto:** Las comparaciones de latencia entre operaciones GraphQL (en ms) y operaciones legacy (en segundos) son incorrectas. Un request de 500ms aparece como `duration: 0`.

---

### void en controllers {#void-controllers}

**Patrón actual:**
```typescript
@MessagePattern(CMDS.logs.trace.create)
createTrace(@Payload() payload: ...) {
  void this._service.createTrace(payload);  // Promise rechazada es descartada
}
```

**Fix:**
```typescript
@MessagePattern(CMDS.logs.trace.create)
async createTrace(@Payload() payload: ...) {
  await this._service.createTrace(payload);  // Propaga el error al framework
}
```

---

### MS comentado en docker-compose {#docker-commented}

**Archivo:** `docker/docker-compose.yml`
El servicio `muvin-ms-logs` está comentado. Solo levanta MySQL.

**Impacto:** No se puede orquestar el stack completo con `docker compose up`.

---

### Dead code: IDENTITY {#dead-identity}

`src/common/functions/identity.ts` exporta `IDENTITY = <T>(x: T): T => x` pero no tiene usos detectados en el proyecto.

---

### Dep innecesaria: platform-express {#platform-express}

`@nestjs/platform-express` está en `dependencies` pero el MS no expone HTTP. Esta dependencia incluye Express.js completo sin necesidad.

---

*Ver también: [[hotspots]] · [[recomendaciones-modernizacion]] · [[security-inventory]]*

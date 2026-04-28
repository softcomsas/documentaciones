# Deuda Técnica

**Última actualización:** 2026-04-27

## Issues Conocidos

### 1. Código Duplicado en Repositories

**Ubicación:** `findOne()` vs `findByReference()`

**Problema:** Lógica similar, sin abstracción común.

**Deuda:** Media | **Esfuerzo:** 1 día

**Solución:**
```typescript
// Consolidar en una búsqueda genérica
private async findOne(where: WhereInput) { ... }
```

---

### 2. DTOs Inline (No Separados)

**Ubicación:** Controladores tienen payloads tipados inline

**Problema:** Difícil de reutilizar, falta validación de schema.

**Deuda:** Media | **Esfuerzo:** 1 día

**Solución:**
```typescript
// Crear archivos separados
contracts.dto.ts
validation.dto.ts
```

---

### 3. Falta de Clase de Excepciones

**Ubicación:** Lanzar strings de error en lugar de excepciones

**Problema:** Difícil de testear, sin tipado.

**Deuda:** Media | **Esfuerzo:** 1-2 días

**Solución:**
```typescript
// Crear excepciones personalizadas
export class ContractNotFoundException extends NotFoundException { }
export class InvalidCuitException extends BadRequestException { }
```

---

### 4. Sin Guards de Autorización

**Ubicación:** Todas las operaciones

**Problema:** Autorización en service, no en middleware.

**Deuda:** Alta | **Esfuerzo:** 2 días

**Solución:**
```typescript
@UseGuards(AuthorizationGuard)
@MessagePattern('commercial.contracts.create')
async create(@Payload() payload) { ... }
```

---

### 5. Logging Inconsistente

**Ubicación:** Mezcla de console.log y LoggerService

**Problema:** Logs no estructurados, difícil de buscar.

**Deuda:** Baja | **Esfuerzo:** 1 día

**Solución:**
- Usar LoggerService en todas partes
- Estructurar logs en JSON
- Centralizar en stack ELK

---

### 6. Tests E2E Limitados

**Ubicación:** test/ folder

**Problema:** Cobertura insuficiente, muchos caminos no testeados.

**Deuda:** Media | **Esfuerzo:** 3-5 días

**Solución:**
- Escribir tests E2E para todos los endpoints
- Tests de error scenarios
- Tests de validación

---

### 7. Sin Migrations Versionadas

**Ubicación:** prisma/migrations

**Problema:** Migraciones manuales, difícil de revertir.

**Deuda:** Baja | **Esfuerzo:** 1 día

**Solución:**
- Usar Prisma migrations automáticas
- Versionarlas en Git
- Scripts de rollback

---

### 8. Resolución CUIT sin Caché

**Ubicación:** ContractsService.createContract

**Problema:** Cada create hace 4-5 queries a entities.

**Deuda:** Media | **Esfuerzo:** 1-2 días

**Solución:**
```typescript
// Implementar caché en-memory
private cuitCache = new Map<string, number>();

async resolveCuit(cuit: string) {
  if (this.cuitCache.has(cuit)) {
    return this.cuitCache.get(cuit);
  }
  // ... query y guardar en caché
}
```

---

### 9. Sin Integración Testing

**Ubicación:** test/

**Problema:** Tests unitarios aislados, sin testing con BD.

**Deuda:** Media | **Esfuerzo:** 2-3 días

**Solución:**
- Integration tests con MySQL de test
- Docker container para tests
- GitHub Actions para CI

---

## Matriz de Deuda

| Issue | Tipo | Severidad | Esfuerzo | ROI |
|-------|------|-----------|----------|-----|
| DTOs duplicados | Code smell | 🟢 Media | 1d | Alto |
| Excepciones faltantes | Code smell | 🟡 Alta | 1d | Medio |
| Logging inconsistente | Config | 🟢 Baja | 1d | Bajo |
| Tests insuficientes | Testing | 🟡 Alta | 3d | Alto |
| Sin guards | Security | 🔴 Crítico | 2d | Crítico |
| Caché faltante | Performance | 🟡 Media | 2d | Alto |

---

## Plan de Pago (Roadmap)

### Sprint 1 (Semana 1-2)
- ✓ Guards de autorización
- ✓ Excepciones personalizadas
- ✓ DTOs separados

### Sprint 2 (Semana 3-4)
- ✓ Tests E2E (50% cobertura)
- ✓ Logging estructurado
- ✓ Caché de CUIT

### Sprint 3 (Semana 5-6)
- ✓ Tests E2E (80%+ cobertura)
- ✓ Integration tests
- ✓ Documentación actualizada

---

## Documentos Relacionados

- [[hotspots|Hotspots Críticos]] - Riesgos de seguridad
- [[recomendaciones-modernizacion|Recomendaciones]] - Futuro
- [[../05-inventarios/tree-estructura-archivos|Árbol de Archivos]] - Ubicación de código


# Hotspots de Complejidad

> **Contexto:** [[deuda-tecnica]] · [[_indice-modulos]]

## Definición

Un hotspot es un archivo o función con alta complejidad ciclomática, múltiples responsabilidades, o alto riesgo de regresión ante cambios. Priorizar para refactoring o cobertura de tests.

## Mapa de hotspots

```mermaid
quadrantChart
    title Complejidad vs. Riesgo de cambio
    x-axis "Baja complejidad" --> "Alta complejidad"
    y-axis "Bajo riesgo" --> "Alto riesgo"
    quadrant-1 "Refactorizar primero"
    quadrant-2 "Cubrir con tests"
    quadrant-3 "Bajo riesgo"
    quadrant-4 "Monitorear"
    LegacyService.create(): [0.85, 0.9]
    LegacyService.update(): [0.75, 0.85]
    LegacyService.searchUser(): [0.70, 0.60]
    constant.ts: [0.15, 0.95]
    PrismaService: [0.20, 0.80]
    terms.ts: [0.55, 0.40]
    json.ts: [0.30, 0.35]
```

## Top 5 hotspots

### 1. `src/modules/legacy/service.ts` — `create()` 🔴

- **Por qué:** Orquesta 5+ operaciones de BD secuenciales con dependencias entre ellas (upsert action → upsert user → insert → compress → update). Dos fases de escritura sobre el mismo registro.
- **Riesgo:** Ventana de inconsistencia entre INSERT y UPDATE del payload. Un error en la compresión deja el registro sin datos.
- **Acción recomendada:** Extraer `_compressAndUpdate()` y `_upsertAction()` como métodos privados con tests unitarios.

### 2. `src/common/cmd/constant.ts` — línea 17 🔴

- **Por qué:** Un typo hace que `updateEvent()` nunca se ejecute. El archivo es pequeño pero su impacto es crítico.
- **Riesgo:** Todos los eventos de la BD tienen `response/duration/finishedAt = null`.
- **Acción recomendada:** Fix inmediato + test de que cada CMD es único (un test trivial que habría prevenido esto).

### 3. `src/service.ts` (PrismaService) — dual instantiation ⚠️

- **Por qué:** Está declarado como `provider` en AMBOS módulos (`MicroservicesModule` y `LegacyModule`) sin ser global ni exportado desde un módulo compartido. Resultan dos instancias de `PrismaClient` en runtime.
- **Riesgo:** Consumo innecesario de conexiones al pool de MySQL. Comportamiento potencialmente inconsistente en transacciones.
- **Acción recomendada:** Crear `DatabaseModule` global con `PrismaService` como provider compartido.

### 4. `src/modules/legacy/service.ts` — `update()` ⚠️

- **Por qué:** Contiene la inconsistencia de unidad de duración (`Math.floor(ms/1000)` = segundos vs. `ms` en traces).
- **Riesgo:** Datos de performance comparativa incorrectos en dashboards de auditoría.
- **Acción recomendada:** Alinear a ms y documentar el campo con un comment en el schema.

### 5. `src/core/utils/terms.ts` — `extractSearchableFieldsFn()` 🟡

- **Por qué:** Recursividad sobre objetos JSON arbitrarios con lookup de aliases. Puede ser lento con payloads grandes y no tiene límite de profundidad.
- **Riesgo:** Bajo en producción actual, pero puede convertirse en un cuello de botella si los payloads crecen.
- **Acción recomendada:** Agregar límite de profundidad y tests de performance con payloads grandes.

---

*Ver también: [[deuda-tecnica]] · [[recomendaciones-modernizacion]] · [[security-inventory]]*

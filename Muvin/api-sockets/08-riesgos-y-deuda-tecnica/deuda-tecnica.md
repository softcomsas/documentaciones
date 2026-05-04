# Deuda Técnica — api-sockets

> [[hotspots]] | [[recomendaciones-modernizacion]]

## DT-SEG — Seguridad

| ID | Descripción | Impacto | Esfuerzo |
|----|-------------|---------|----------|
| DT-SEG-01 | Sin autenticación en endpoints REST | 🔴 Crítico | Medio |
| DT-SEG-02 | CORS completamente abierto (`origin: true`) | 🔴 Alto | Bajo |
| DT-SEG-03 | `id_persona` auto-declarado sin validación JWT | 🟠 Medio | Alto |
| DT-SEG-04 | Sin rate limiting (WS ni REST) | 🟠 Medio | Bajo |

## DT-FIAB — Fiabilidad

| ID | Descripción | Impacto | Esfuerzo |
|----|-------------|---------|----------|
| DT-FIAB-01 | In-memory no se limpia en disconnect | 🔴 Alto | Bajo |
| DT-FIAB-02 | Bug lógico inicialización Redis (`null` parse) | 🔴 Alto | Bajo |
| DT-FIAB-03 | Doble almacenamiento (Redis vs in-memory) inconsistente | 🟠 Medio | Alto |
| DT-FIAB-04 | `configurar-usuario` múltiple crea duplicados en Redis | 🟠 Medio | Bajo |
| DT-FIAB-05 | Redis cliente en `server.ts` no conectado (código muerto) | 🟡 Bajo | Bajo |
| DT-FIAB-06 | Sin manejo de error si Redis está caído | 🔴 Alto | Medio |

## DT-MANT — Mantenibilidad

| ID | Descripción | Impacto | Esfuerzo |
|----|-------------|---------|----------|
| DT-MANT-01 | Socket.IO v2 (EOL, versión actual: v4) | 🟠 Medio | Alto |
| DT-MANT-02 | Typo `clientRedist` en `server.ts` | 🟡 Bajo | Bajo |
| DT-MANT-03 | Typo "No esiste" en mensaje de error (`router.ts`) | 🟡 Bajo | Bajo |
| DT-MANT-04 | Sin tests (unitarios ni de integración) | 🟠 Medio | Alto |
| DT-MANT-05 | `POST /chofer/:id_chofer` hardcoded con mensaje de prueba | 🟡 Bajo | Bajo |
| DT-MANT-06 | `node:current-alpine3.16` en Dockerfile (tag mutable) | 🟡 Bajo | Bajo |

## Prioridad de resolución

```
Inmediato (< 1 semana):
  DT-FIAB-01, DT-FIAB-02, DT-FIAB-05, DT-SEG-02

Corto plazo (< 1 mes):
  DT-SEG-01, DT-SEG-04, DT-FIAB-04, DT-FIAB-06, DT-MANT-02, DT-MANT-03

Largo plazo (roadmap):
  DT-SEG-03, DT-FIAB-03, DT-MANT-01, DT-MANT-04
```

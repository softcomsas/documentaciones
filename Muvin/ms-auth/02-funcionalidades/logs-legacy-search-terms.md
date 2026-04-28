# Funcionalidad: Buscar Logs por Términos

> **Módulo:** [[modulo-logs]]
> **CMD:** `logs.legacy.search.terms`
> **Tipo:** Consulta / Reporte / RPC send

## Descripción funcional

Realiza una búsqueda textual sobre los registros de log del sistema legacy. Permite encontrar logs por endpoint, payload u otros campos textuales.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| ⚠️ Pendiente de verificar | — | — | Ver `src/contracts/logs/interfaces/legacy.ts` |

## Respuesta esperada

```typescript
{ success: true, data: ILogLegacy[] }
```

## Riesgos

- ⚠️ Búsqueda textual sin índices puede ser costosa con alto volumen de logs.

## Archivos fuente relevantes

- `src/contracts/logs/interfaces/legacy.ts` (interfaz `search-terms`)

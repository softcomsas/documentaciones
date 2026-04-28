# Funcionalidad: Buscar Contrato por Referencia

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.search.one`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Recupera un contrato específico identificado por la combinación de compañía y referencia.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `company` | `number` | Sí | ID de la compañía |
| `reference` | `string` | Sí | Referencia única del contrato |

## Respuesta esperada

```typescript
{ success: true, data: IContract }
```

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `search-one`)

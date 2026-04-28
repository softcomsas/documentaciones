# Funcionalidad: Listar Contratos por Cliente

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.search.list`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Retorna una lista de contratos filtrados por compañía, cliente y código de producto. Útil para mostrar todos los contratos activos de un cliente en particular.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `company` | `number` | Sí | ID de la compañía |
| `client` | `number` | Sí | ID del cliente |
| `code` | `number` | Sí | Código del producto/servicio |

## Respuesta esperada

```typescript
{ success: true, data: IContract[] }
```

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `search-list`)

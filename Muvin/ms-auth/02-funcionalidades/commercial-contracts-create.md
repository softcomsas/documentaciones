# Funcionalidad: Crear Contrato Comercial

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.create`
> **Tipo:** CRUD / RPC send

## Descripción funcional

Crea un nuevo contrato comercial en el sistema. Un contrato define la relación entre una compañía, un cliente, un broker y las condiciones operativas (límite, volumen, precio, fechas, estado, prioridad).

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `reference` | `string` | Sí | Referencia única del contrato |
| `company` | `number` | Sí | ID de la compañía |
| `client` | `number` | Sí | ID del cliente |
| `broker` | `number` | Sí | ID del broker |
| `destination` | `number` | Sí | ID del destino |
| `code` | `number` | Sí | Código del producto/servicio |
| `limit` | `number` | Sí | Límite del contrato |
| `volume` | `number` | Sí | Volumen contratado |
| `price` | `number` | Sí | Precio |
| `start` | ⚠️ Pendiente tipo | Sí | Fecha de inicio |
| `end` | ⚠️ Pendiente tipo | Sí | Fecha de fin |
| `status` | `TCommercialContractStatus` | No | Estado inicial (default: OPEN presumiblemente) |
| `priority` | `TCommercialContractPriority` | No | Prioridad del contrato |

## Respuesta esperada

```typescript
{ success: true, data: IContract }
```

## Datos que lee/escribe

- **Escribe:** [[entidad-contract]] (en ms-commercial vía TCP)

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `create`)

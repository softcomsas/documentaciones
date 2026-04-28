# Funcionalidad: Cambiar Límite de Contrato

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.change.limit`
> **Tipo:** CRUD / RPC send

## Descripción funcional

Modifica el límite operativo de un contrato comercial existente. Operación de escritura — impacta directamente en las condiciones del contrato.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `company` | `number` | Sí | ID de la compañía |
| `reference` | `string` | Sí | Referencia del contrato a modificar |
| `limit` | `number` | Sí | Nuevo valor del límite |

## Respuesta esperada

```typescript
{ success: true, data: IContract }
```

## Riesgos

- ⚠️ No hay validación de rangos para el nuevo límite en el contrato — podría setearse en 0 o negativo.

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `change-limit`)

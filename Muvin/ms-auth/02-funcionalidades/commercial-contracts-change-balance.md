# Funcionalidad: Cambiar Balance de Contrato

> **Módulo:** [[modulo-commercial]]
> **CMD:** `commercial.contracts.change.balance`
> **Tipo:** CRUD / RPC send

## Descripción funcional

Modifica el balance actual de un contrato comercial. Probablemente se invoca al registrar operaciones que consumen o liberan saldo del contrato.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `company` | `number` | Sí | ID de la compañía |
| `reference` | `string` | Sí | Referencia del contrato |
| `balance` | `number` | Sí | Nuevo balance o delta a aplicar (⚠️ Pendiente de verificar si es absoluto o diferencial) |

## Respuesta esperada

```typescript
{ success: true, data: IContract }
```

## Riesgos

- ⚠️ No está claro si el valor es absoluto (reemplaza el balance) o diferencial (suma/resta). Verificar en `ms-commercial`.
- ⚠️ Sin validación de balance negativo en el contrato.

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts` (interfaz `change-balance`)

# Entidad: Contract

> **BD:** ms-commercial (remota — acceso vía TCP)
> **Fuente del contrato:** `src/contracts/commercial/interfaces/contracts.ts`
> **Nota:** Esta entidad no vive en la BD de ms-auth. Se accede exclusivamente a través de comandos TCP hacia ms-commercial.

---

## Descripción

Representa un contrato comercial del ecosistema Muvin. Define las condiciones operativas entre una compañía, un cliente, un broker y un destino, incluyendo límites, volúmenes, precios y vigencia temporal.

---

## Campos (según contrato TypeScript)

| Campo | Tipo | Nulable | Descripción |
|-------|------|---------|-------------|
| `reference` | `string` | No | Referencia única del contrato (PK funcional) |
| `company` | `number` | No | FK a Company |
| `client` | `number` | No | ID del cliente |
| `broker` | `number` | No | ID del broker |
| `destination` | `number` | No | ID del destino |
| `code` | `number` | No | Código del producto/servicio |
| `limit` | `number` | No | Límite operativo del contrato |
| `volume` | `number` | No | Volumen contratado |
| `price` | `number` | No | Precio pactado |
| `start` | ⚠️ pendiente | No | Fecha de inicio de vigencia |
| `end` | ⚠️ pendiente | No | Fecha de fin de vigencia |
| `status` | `TCommercialContractStatus` | Sí | Estado: OPEN, CLOSED, EXPIRED, VOIDED |
| `priority` | `TCommercialContractPriority` | Sí | Prioridad: HIGHEST, HIGH, MEDIUM, LOW, LOWEST |

---

## Módulos que la consumen

- [[modulo-commercial]] — todas las operaciones CRUD y de consulta

---

## Archivos fuente relevantes

- `src/contracts/commercial/interfaces/contracts.ts`

# Funcionalidad: Buscar Múltiples Compañías

> **Módulo:** [[modulo-auth]]
> **CMD:** `auth.companies.search.all`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Permite recuperar múltiples compañías en una sola llamada, a partir de un array de IDs. Útil para inicializar listas o validar conjuntos de compañías en batch.

## Precondiciones

- Se debe enviar al menos un ID en el array.

## Flujo principal

```mermaid
flowchart TD
    A([Consumer envía TCP: auth.companies.search.all]) --> B[Handler recibe payload\n{ids: number[]}]
    B --> C[PrismaService: buscar Companies donde id IN ids]
    C --> D[Retorna ICompany[]\n{id, cuit, rs}[]]
    D --> E([Respuesta TCP al consumer])
```

> [!warning] Flujo esperado según contrato — handler no implementado.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `ids` | `number[]` | Sí | Array de IDs de compañías a buscar |

## Respuesta esperada

```typescript
{ success: true, data: ICompany[] }
```

## Datos que lee/escribe

- **Lee:** [[entidad-company]]

## Archivos fuente relevantes

- `src/contracts/auth/interfaces/companies.ts` (interfaz `search-all`)
- `src/common/cmd/interfaces/auth.ts`

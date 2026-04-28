# Funcionalidad: Buscar Compañía por ID

> **Módulo:** [[modulo-auth]]
> **CMD:** `auth.companies.search.one`
> **Tipo:** Consulta / RPC send

## Descripción funcional

Permite buscar una compañía registrada en el sistema a partir de su identificador único. Retorna los datos básicos de la compañía: ID, CUIT y razón social.

## Precondiciones

- El ID de compañía debe existir en la base de datos.

## Flujo principal

```mermaid
flowchart TD
    A([Consumer envía TCP: auth.companies.search.one]) --> B[Handler recibe payload\n{id: number}]
    B --> C[PrismaService: buscar Company por id]
    C --> D{¿Encontrado?}
    D -->|Sí| E[Retorna ICompany\n{id, cuit, rs}]
    D -->|No| F[Retorna error\n{success: false}]
    E --> G([Respuesta TCP al consumer])
    F --> G
```

> [!warning] Flujo esperado según contrato — handler no implementado.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `id` | `number` | Sí | ID único de la compañía |

## Respuesta esperada

```typescript
{ success: true, data: ICompany }
// ICompany: { id: number, cuit: string, rs: string }
```

## Servicios backend invocados

Ninguno externo — consulta directamente a MySQL vía Prisma.

## Datos que lee/escribe

- **Lee:** [[entidad-company]]

## Archivos fuente relevantes

- `src/contracts/auth/interfaces/companies.ts` (interfaz `search-one`)
- `src/common/cmd/interfaces/auth.ts` (constante `auth.companies.search.one`)

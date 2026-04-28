# Funcionalidad: Crear Clave API

> **Módulo:** [[modulo-auth]]
> **CMD:** `auth.create.key`
> **Tipo:** Seguridad / CRUD / RPC send

## Descripción funcional

Genera un nuevo par de credenciales (key + secret) para una compañía dada. Las claves API son el mecanismo de autenticación principal del ecosistema Muvin para requests entre servicios y desde sistemas externos.

## Precondiciones

- La compañía referenciada debe existir (`company` válido).
- El caller debe tener permisos para crear claves (validación pendiente de implementar).

## Flujo principal

```mermaid
flowchart TD
    A([Consumer envía TCP: auth.create.key]) --> B[Handler recibe payload\n{company: number}]
    B --> C[Generar key aleatoria]
    C --> D[Generar secret aleatorio]
    D --> E[PrismaService: INSERT Key\n{key, secret, company, active:true}]
    E --> F[Retorna IKey\n{id, key, secret, company, active}]
    F --> G([Respuesta TCP al consumer])
```

> [!warning] Flujo esperado según contrato — handler no implementado.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `company` | `number` | Sí | ID de la compañía a la que se le asigna la clave |

## Respuesta esperada

```typescript
{ success: true, data: IKey }
// IKey: { id: number, key: string, secret: string, company: number, active: boolean }
```

## Riesgos

- 🔒 El `secret` debe almacenarse hasheado (bcrypt, argon2 u equivalente). Sin implementación no es verificable.
- 🔒 No hay TTL ni expiración de claves en el contrato actual.
- ⚠️ No hay mecanismo de rotación de claves definido.

## Datos que lee/escribe

- **Escribe:** [[entidad-key]]
- **Lee:** [[entidad-company]] (para validar que existe)

## Archivos fuente relevantes

- `src/contracts/auth/interfaces/validation.ts` (interfaz `create-key`)
- `src/common/cmd/interfaces/auth.ts`

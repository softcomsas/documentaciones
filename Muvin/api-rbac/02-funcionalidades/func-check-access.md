# Funcionalidad: Verificación de Acceso a Servicio

## Descripción

**Endpoint central del sistema RBAC.** Dado un `id_user`, un `service` (identificador de endpoint) y un `type` (verbo HTTP), retorna `true` si el usuario tiene acceso o `false` si no.

Este endpoint es consumido por todos los demás microservicios de la plataforma para autorizar operaciones.

## Flujo

```mermaid
flowchart TD
    MS[Microservicio externo] -->|POST check-access-to-service\n{id_user, service, type}| UC[UserController]
    UC --> CAS[CheckAccessToService.validate]
    CAS -->|Inválido| E422[HTTP 422]
    CAS -->|Válido| RP[RolePermission.checkAccess]
    RP --> DB[(SELECT EXISTS\nJOIN user_role\nJOIN permission\nWHERE id_user + service + type)]
    DB -->|true| T[Retorna true]
    DB -->|false| F[Retorna false]
```

## SQL generado

```sql
SELECT EXISTS (
  SELECT 1 
  FROM role_permission t
  INNER JOIN permission p ON p.id = t.id_permission
  INNER JOIN user_role ur ON ur.id_role = t.id_role
  WHERE ur.id_user = :id_user
    AND p.service = :service
    AND p.type = :type
    AND ur.deleted_at IS NULL
    AND p.deleted_at IS NULL
    AND t.deleted_at IS NULL
) AS result
```

## Request

```json
POST /user/check-access-to-service
Authorization: {seed}
Content-Type: application/json

{
  "id_user": 42,
  "service": "api-bus/pedidos",
  "type": "POST"
}
```

## Response

```json
true
```
o
```json
false
```

## Convención de `service`

El campo `service` es un string libre que identifica el endpoint/recurso en el microservicio destino. La convención debe acordarse entre los equipos. Ejemplo: `"pedidos/crear"`, `"chofer-app/postularme"`.

## Notas de uso

- Todos los microservicios deben llamar este endpoint **antes** de ejecutar operaciones sensibles.
- El resultado es un boolean puro — la lógica de qué hacer si es `false` (retornar 401/403) es responsabilidad del microservicio llamante.

## Referencias

- [[modulo-user-controller]]
- [[modulo-form-models#check-access]]
- [[modulo-models#role-permission]]

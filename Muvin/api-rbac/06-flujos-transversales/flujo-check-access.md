# Flujo: Check-Access desde Microservicio Externo

## Descripción

El flujo más importante del sistema. Un microservicio externo verifica si un usuario tiene permiso para ejecutar una operación.

## Diagrama completo

```mermaid
sequenceDiagram
    actor User as Usuario final
    participant MS as Microservicio (api-bus, api, etc.)
    participant RBAC as api-rbac
    participant DB as MySQL

    User->>MS: Request a endpoint protegido
    MS->>RBAC: POST /user/check-access-to-service\nAuthorization: {seed}\n{id_user, service, type}
    
    RBAC->>RBAC: StrongTokenAuth.validate(seed)
    RBAC->>RBAC: CheckAccessToService.validate()
    
    RBAC->>DB: SELECT EXISTS(\n  role_permission JOIN user_role\n  JOIN permission\n  WHERE id_user + service + type\n)
    DB-->>RBAC: true | false
    RBAC-->>MS: true | false

    alt true
        MS->>MS: Ejecutar operación
        MS-->>User: Respuesta normal
    else false
        MS-->>User: HTTP 403 Forbidden
    end
```

## Convención de `service` y `type`

Cada microservicio define sus propios valores de `service` al registrar permisos. Ejemplo:

| service | type | Descripción |
|---------|------|-------------|
| `pedidos/crear` | POST | Crear un pedido |
| `chofer-app/postularme` | POST | Postularse a una carga |
| `usuarios/admin` | DELETE | Eliminar usuarios |

## Flujo: Login + uso de JWT

```mermaid
sequenceDiagram
    actor U as Usuario (app-panel)
    participant RBAC as api-rbac

    U->>RBAC: POST /user/login\n{username, password}
    RBAC-->>U: {id, name, token: "eyJ..."}
    
    U->>RBAC: POST /user/decode-token\n{token: "eyJ..."}
    RBAC-->>U: {id: 42}
    
    Note over U,RBAC: El id del usuario se usa\nen check-access-to-service
```

## Flujo: Setup RBAC completo

```mermaid
flowchart TD
    A[Crear Permission\nPOST /permission\n{name, service, type}] --> B
    B[Crear Role\nPOST /role\n{name}] --> C
    C[Vincular Role+Permission\nPOST /role-permission\n{id_role, id_permission}] --> D
    D[Crear User\nPOST /user\n{name, password}] --> E
    E[Vincular User+Role\nPOST /user-role\n{id_user, id_role}] --> F
    F[✅ check-access-to-service retorna true]
```

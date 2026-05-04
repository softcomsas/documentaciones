# Glosario — api-rbac

| Término | Definición |
|---------|------------|
| **RBAC** | Role-Based Access Control — modelo de control de acceso donde los permisos se asignan a roles y los roles a usuarios |
| **Permission** | Permiso atómico: combinación única de `service` (string identificador de microservicio/endpoint) + `type` (verbo HTTP: GET/POST/PUT/DELETE) |
| **Role** | Agrupación lógica de permisos. Un rol puede tener N permisos. |
| **User** | Usuario del sistema con nombre único y contraseña hasheada (bcrypt). |
| **UserRole** | Tabla de asociación M:N entre User y Role. Soporta soft delete. |
| **RolePermission** | Tabla de asociación M:N entre Role y Permission. Soporta soft delete. |
| **Soft delete** | Patrón donde los registros nunca se borran físicamente; se marca `deleted_at` con timestamp Unix. |
| **StrongTokenAuth** | Componente custom de Yii2 que valida el header `Authorization` comparando contra un `seed` estático configurado. Es el guard de inter-servicios. |
| **JwtToken** | Componente custom que genera y decodifica tokens JWT HS512 usando `sizeg/yii2-jwt`. El payload contiene solo `id` del usuario. |
| **check-access-to-service** | Endpoint central del sistema RBAC: dado `id_user + service + type`, retorna `true/false` consultando la cadena `user → user_role → role_permission → permission`. |
| **seed** | Valor estático configurado en `StrongTokenAuth` que actúa como API key para comunicación entre microservicios. |
| **ActiveDataProvider** | Clase Yii2 para paginación automática de resultados de consultas ActiveRecord. |
| **TimestampBehavior** | Behavior de Yii2 que auto-rellena `created_at` y `updated_at` con timestamp Unix en cada save. |
| **scenario** | Mecanismo de Yii2 para definir qué atributos son asignables en masa según el contexto (create vs update). |
| **Lot** | Sufijo en modelos (`RolePermissionsLot`, `UserRolesLot`) que indica operaciones en lote — reciben array de objetos para crear/eliminar múltiples registros en una sola llamada. |

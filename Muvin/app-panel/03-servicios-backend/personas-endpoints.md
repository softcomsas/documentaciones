# Endpoints: Personas y Usuarios

> **Servicios:** `PersonasService`, `UserService`
> **API:** `GlobalService.apiHost` + integraciones externas
> **Consumido por:** [[modulo-admin]], todos los módulos (datos de personas)

---

## PersonasService

> **Archivo:** `src/app/shared/services/personas.service.ts`
> **Líneas:** ~850+
> **Endpoints:** ~48
> **Patrón especial:** Caching con `Map<string, {data, lastPage}>`

### CRUD de usuarios

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `usuario` | Listar usuarios (paginado) |
| GET | `usuario/centro` | Usuarios de un centro |
| POST | `usuario` | Crear usuario |
| PUT | `usuario/{id}` | Actualizar usuario |
| DELETE | `usuario/{id}` | Eliminar usuario |
| PUT | `usuario/change-password` | Cambiar contraseña |

### Personas por rol

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `persona-rol/roles` | Roles de una persona (por CUIT) |
| GET | `persona-rol/comprador-by-razon-social` | Buscar comprador (con cache) |
| GET | `persona-rol/vendedor-by-razon-social` | Buscar vendedor (con cache) |
| GET | `persona-rol/comprador-by-cuit` | Buscar comprador por CUIT |
| GET | `persona-rol/vendedor-by-cuit` | Buscar vendedor por CUIT |
| GET | `persona-rol/nombre-by-cuit` | Nombre por CUIT |

### Actores logísticos (por tipo)

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `chofer` | Listar choferes (paginado) |
| GET | `entregador` | Listar entregadores |
| GET | `intermediario` | Listar intermediarios |
| GET | `operador` | Listar operadores |
| GET | `destinatario` | Listar destinatarios |
| GET | `transportista` | Listar transportistas |
| GET | `corredor` | Listar corredores |
| GET | `dador` | Listar dadores |

### Integración ERP

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `erp/filtrar/get-productores` | Productores desde ERP |
| GET | `gateway/api/commercial/contracts/list` | Contratos desde UAA (gateway externo) |

### Validación CUIT

PersonasService incluye algoritmo de validación de CUIT/CUIL de 11 dígitos (cliente-side):
- Valida dígito verificador
- Acepta tipos 20, 23, 24, 27, 30, 33, 34

> [!info] Cache de personas
> `PersonasService` implementa un cache en memoria (`Map<string, {data, lastPage}>`) para búsquedas de compradores y vendedores. El cache es consciente de paginación (`lastPage`). No tiene TTL — se invalida solo al recargar la página.

---

## UserService {#user}

> **Archivo:** `src/app/shared/services/user.service.ts`
> **Líneas:** ~100
> **Endpoints:** ~10

| Verbo | Ruta | Propósito |
|---|---|---|
| GET | `user/profile` | Perfil del usuario logueado |
| GET | `user/permissions` | Permisos del usuario |
| PUT | `user/change-password` | Cambiar contraseña |
| GET | `user/centros` | Centros del usuario |
| PUT | `user/profile` | Actualizar perfil |

---

## Archivos fuente

- `src/app/shared/services/personas.service.ts` (~850 líneas)
- `src/app/shared/services/user.service.ts` (~100 líneas)

---

## Referencias

- [[_indice-servicios]] — Índice general
- [[auth-endpoints]] — Autenticación
- [[modulo-admin]] — ABM de personas y usuarios

# Endpoints del dominio: persona-rol

> **URL base:** `{LEGACY_MICROSERVICE_BASE_URL}persona-rol/`
> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

---

## `GET /persona-rol/comprador-by-razon-social`

**Propósito:** Buscar compradores registrados en el sistema legacy por nombre de razón social, con paginación.
**Autenticación:** ⚠️ Pendiente de verificar
**Consumido por:** [[modulo-service]] → [[api-comprador-by-razon-social]]
**Fuente en código:** `src/api/queries/comprador-by-razon-social.ts`

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|--------|-----------|------|-------------|-------------|
| `razonSocial` | query string | `string` | ⚠️ No validado | Nombre o parte del nombre de la empresa a buscar |
| `page` | query string | `number` | ⚠️ No validado | Número de página para la paginación |

> Los parámetros se construyen en `AppService.#url()` a partir de `params.queryParams`. No existe validación de schema sobre ellos en la capa del microservicio.

### Payload

No aplica (método GET, sin cuerpo).

### Respuesta del backend legacy (cruda)

```json
{
  "data": [
    {
      "id": 123,
      "razonSocial": "Empresa S.A.",
      "cuitCuil": "30-12345678-9"
    }
  ],
  "error": null,
  "meta": {
    "total": 45,
    "page": 1,
    "count": 10
  }
}
```

### Respuesta normalizada (retornada al consumidor)

```json
{
  "code": 200,
  "data": [
    {
      "id": 123,
      "rs": "Empresa S.A.",
      "cuit": "30-12345678-9"
    }
  ]
}
```

> [!warning] Paginación no propagada
> El campo `meta` de la respuesta del backend legacy (`total`, `page`, `count`) **no se incluye** en la respuesta normalizada `IApiResponse`. El consumidor no puede saber cuántas páginas existen. Ver [[deuda-tecnica]].

### Respuestas de error

| Código | Situación | Respuesta normalizada |
|--------|-----------|-----------------------|
| 4xx | Error del cliente (ej. params inválidos) | `{ code: <status>, data: [] }` |
| 5xx | Error del servidor legacy | `{ code: 500, data: [] }` |
| Sin respuesta / Timeout | Backend no disponible | `{ code: 500, data: [] }` |

> [!warning] Pérdida de información de error
> El adapter de error solo retorna `{ code, data: [] }`. No hay campo `message` ni `error` en la respuesta. El consumidor no puede distinguir entre "sin resultados" (200 con data vacío) y "error del servidor" sin revisar el `code`.

### Efectos secundarios

Ninguno. Es una operación de solo lectura en el backend legacy.

---

## `GET /persona-rol/comprador-by-cuit`

**Estado:** ⚠️ **No implementado en este microservicio.**
**Declarado en:** `src/types/endpoints.ts` como parte de `TEndpoint`
**Sin implementar en:** `src/api/interface.ts`, `src/api/map.ts`, `src/api/queries/`

### Descripción funcional esperada

Basándose en el patrón de `comprador-by-razon-social`, este endpoint debería:
- Recibir un `cuit` como query param
- Llamar a `GET {BASE_URL}persona-rol/comprador-by-cuit?cuit=X`
- Retornar un array de compradores que coincidan con ese CUIT

> [!danger] Endpoint en estado inconsistente
> El tipo `TEndpoint` incluye esta ruta, lo que sugiere intención de implementación. Sin embargo, no existe la query, no está en `IQueries` ni en `QUERIES_MAP`. Si un consumidor intenta usar la clave `'comprador-by-cuit'`, el sistema lanzará `"No endpoint found for query"` en tiempo de ejecución. Para forzar la inconsistencia a tiempo de compilación, la clave tampoco está en `IRequests`.

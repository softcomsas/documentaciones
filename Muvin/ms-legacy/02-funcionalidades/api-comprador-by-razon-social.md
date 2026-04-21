# Funcionalidad: Búsqueda de compradores por razón social

> **Módulo:** [[modulo-api]]
> **Clave de mensaje TCP:** `comprador-by-razon-social`
> **Tipo:** Integración / Proxy de consulta

## Descripción funcional

Permite a cualquier microservicio del ecosistema Muvin buscar entidades "comprador" registradas en el sistema backend legacy, filtrando por razón social (nombre de empresa) con soporte de paginación. La búsqueda se delega íntegramente al backend REST legacy; este microservicio actúa como traductor del protocolo TCP al HTTP.

El resultado es un array de objetos simplificados con `id`, `rs` (razón social normalizada) y `cuit`.

## Precondiciones

- El microservicio `muvin-ms-legacy` debe estar activo y alcanzable por TCP.
- La variable `LEGACY_MICROSERVICE_BASE_URL` debe apuntar al ambiente correcto (dev/prod).
- El backend legacy debe estar disponible en la URL configurada.
- El consumidor debe conocer el `cmd` configurado (`LEGACY_MICROSERVICE_CMD`, default `CMD-LEGACY`).

## Flujo principal

```mermaid
flowchart TD
    A([Microservicio consumidor envía\nTCP { cmd, endpoint: 'comprador-by-razon-social',\nparams: { queryParams: { razonSocial, page } } }])
    B[AppController recibe el mensaje]
    C[AppService.request 'comprador-by-razon-social', params]
    D[QUERIES_MAP.get retorna la definición de query]
    E[Construir URL:\nBASE_URL + 'persona-rol/comprador-by-razon-social'\n+ ?razonSocial=X&page=Y]
    F[HTTP GET al backend legacy]
    G{¿Respuesta exitosa?}
    H["response adapter:\n{ code: 200, data: [{id, rs, cuit}...] }"]
    I["err adapter:\n{ code: 500, data: [] }"]
    J([Retornar IApiResponse al consumidor])

    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
    G -->|Sí 2xx| H
    G -->|No / Error| I
    H --> J
    I --> J
```

## Flujos alternativos / excepciones

- **Query key no encontrada en el map:** `AppService` lanza `Error("No endpoint found for query: comprador-by-razon-social")`. Propagado como error genérico por el controlador.
- **Backend legacy no disponible (timeout 5s):** el adapter de error captura el `AxiosError` y retorna `{ code: 500, data: [] }`.
- **Backend legacy responde 4xx:** el adapter de error captura el error y retorna `{ code: <status_del_error>, data: [] }`. Nota: `err.status` puede ser `undefined` si Axios no lo provee, en cuyo caso se usa `500`.

## Validaciones de negocio

| Validación | Comportamiento | Ubicación en código |
|------------|----------------|---------------------|
| Payload whitelist | El `ValidationPipe` rechaza campos no declarados | `src/main.ts` (global pipe) |
| Presencia de query params | Sin validación explícita de `razonSocial` o `page` | ⚠️ No hay schema de validación en la capa de query |

> [!warning] Sin validación de query params
> No existe validación de que `razonSocial` o `page` estén presentes antes de llamar al backend legacy. Si se omiten, la llamada HTTP se realiza sin esos parámetros y el comportamiento depende del backend legacy.

## Servicios backend invocados

| Paso | Verbo | Ruta completa | Payload | Respuesta resumida |
|------|-------|---------------|---------|-------------------|
| 1 | GET | `{BASE_URL}persona-rol/comprador-by-razon-social?razonSocial=X&page=Y` | — | `{ data: [{id, razonSocial, cuitCuil}], error: null, meta: {total, page, count} }` |

Ver detalle en [[persona-rol-endpoints#GET-comprador-by-razon-social]].

## Transformación de datos (adapter)

El adapter `response` en `src/api/queries/comprador-by-razon-social.ts` aplica las siguientes transformaciones:

| Campo del backend legacy | Campo normalizado | Tipo |
|--------------------------|-----------------|------|
| `id` | `id` | `number` |
| `razonSocial` | `rs` | `string` |
| `cuitCuil` | `cuit` | `string` |

Los campos del backend legacy `meta` (paginación) **no se propagan** al consumidor en la respuesta actual.

> [!warning] Paginación perdida
> El backend legacy retorna `meta: { total, page, count }` pero el adapter de respuesta en `comprador-by-razon-social.ts` no mapea estos datos al `IApiResponse.meta`. El consumidor no recibe información de paginación. Ver [[deuda-tecnica]].

## Datos que lee/escribe

- **Lee:** Datos de compradores desde el backend legacy (ver [[entidad-comprador]])
- **Escribe:** Nada. Es una operación de solo lectura.

## Archivos fuente relevantes

- `src/api/queries/comprador-by-razon-social.ts`
- `src/api/map.ts`
- `src/api/interface.ts`
- `src/contracts/ms-legacy/interfaces/comprador-by-razon-social.ts`
- `src/controller.ts`
- `src/service.ts`

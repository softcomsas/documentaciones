# Flujo: Request proxy end-to-end

> **Tipo:** `sequenceDiagram` + `stateDiagram-v2`
> **Módulos involucrados:** [[modulo-controller]], [[modulo-service]], [[modulo-api]], Backend Legacy

## Descripción

Ciclo de vida completo de una solicitud desde que un microservicio consumidor envía un mensaje TCP hasta que recibe la respuesta normalizada. Este flujo es el único flujo de negocio del microservicio.

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant CONS as Microservicio consumidor
    participant CTRL as AppController
    participant PIPE as ValidationPipe
    participant SVC as AppService
    participant MAP as QUERIES_MAP
    participant HTTP as HttpService (Axios)
    participant LEGACY as Backend Legacy REST

    CONS->>CTRL: TCP { cmd: "CMD-LEGACY",<br/>endpoint: "comprador-by-razon-social",<br/>params: { queryParams: { razonSocial, page } } }
    CTRL->>PIPE: Valida payload (whitelist)
    PIPE-->>CTRL: Payload válido
    CTRL->>SVC: request("comprador-by-razon-social", params)
    SVC->>MAP: get("comprador-by-razon-social")
    MAP-->>SVC: { method:"GET", endpoint:"persona-rol/comprador-by-razon-social", payload, response, err }
    SVC->>SVC: #url() → "https://…/persona-rol/comprador-by-razon-social?razonSocial=X&page=Y"
    SVC->>SVC: payload(undefined) → undefined (IDENTITY, no hay body en GET)
    SVC->>HTTP: get<IRes>(url)
    HTTP->>LEGACY: GET /persona-rol/comprador-by-razon-social?razonSocial=X&page=Y
    LEGACY-->>HTTP: 200 { data: [{id, razonSocial, cuitCuil}], error: null, meta: {…} }
    HTTP-->>SVC: AxiosResponse<IRes>
    SVC->>SVC: response(axiosRes) → { code: 200, data: [{id, rs, cuit}] }
    SVC-->>CTRL: Observable<IApiResponse>
    CTRL->>CTRL: firstValueFrom(observable)
    CTRL-->>CONS: { code: 200, data: [{id, rs, cuit}] }
```

## Diagrama de estados de la request

```mermaid
stateDiagram-v2
    [*] --> RecibidoPorTCP: Mensaje TCP llega

    RecibidoPorTCP --> ValidandoPayload: ValidationPipe actúa
    ValidandoPayload --> PayloadInvalido: Campo no permitido
    ValidandoPayload --> QueryKeyLookup: Payload válido

    PayloadInvalido --> [*]: Error de validación

    QueryKeyLookup --> QueryNoEncontrada: key no está en QUERIES_MAP
    QueryKeyLookup --> ConstruyendoURL: key encontrada

    QueryNoEncontrada --> ErrorGenerico: throw Error "No endpoint found"

    ConstruyendoURL --> LlamadaHTTP: URL construida

    LlamadaHTTP --> RespuestaExitosa: 2xx del backend legacy
    LlamadaHTTP --> ErrorHTTP: 4xx/5xx o timeout

    RespuestaExitosa --> AdaptandoRespuesta: response adapter
    ErrorHTTP --> AdaptandoError: err adapter

    AdaptandoRespuesta --> RetornandoAlConsumidor: IApiResponse con data
    AdaptandoError --> RetornandoAlConsumidor: IApiResponse con code de error y data vacío

    ErrorGenerico --> ErrorGenericoFinal: AppController catch → new Error()
    ErrorGenericoFinal --> [*]: Error sin contexto al consumidor

    RetornandoAlConsumidor --> [*]
```

## Puntos de falla identificados

| Punto | Causa | Comportamiento actual | Severidad |
|-------|-------|----------------------|-----------|
| `ValidationPipe` rechaza | Campo extra en payload | NestJS lanza `BadRequestException` | 🟡 Manejado |
| Query key no en MAP | Clave inválida | `throw Error("No endpoint found")` → catch genérico | 🟡 |
| Backend legacy timeout (5s) | Backend caído o lento | `err adapter` → `{ code: 500, data: [] }` | 🟡 Parcialmente manejado |
| Error en el `catch` del controller | Cualquier excepción | `throw new Error()` sin contexto — perde stack trace | 🔴 |
| `console.log` del payload | Debug no sanitizado | Datos sensibles en logs del contenedor | 🔴 |

# Flujo: Logging de Request Legado

> **Contexto:** [[_indice-flujos]] · [[modulo-legacy]]
> **Actores:** Sistema legado o gateway proxy, ms-logs (TCP receiver), MySQL

## Descripción

Cada request HTTP hacia un sistema legado genera **2 mensajes TCP** hacia ms-logs:
1. `legacy.create` — al inicio del request (antes de enviarlo al sistema legado)
2. `legacy.update` — al recibir la respuesta del sistema legado

El proceso incluye Brotli compression, extracción de términos de búsqueda (dominio agrícola) y mantenimiento automático de catálogo de endpoints y mirror de usuarios.

## Diagrama de secuencia

```mermaid
sequenceDiagram
    actor U as Usuario
    participant P as Sistema proxy / gateway
    participant SL as Sistema Legado (PANEL/DESCARGAS)
    participant ML as ms-logs (TCP)
    participant DB as MySQL

    U->>P: Request HTTP
    P->>ML: emit('logs.legacy.create', {api, hash, endpoint, method, payload, user?})
    activate ML
    ML->>DB: upsert legacy_actions (catálogo)
    ML->>DB: upsert users + legacy_user_actions (si user)
    ML->>DB: INSERT legacy_panel/descargas (status=PENDING, payload=null)
    ML->>ML: compressJsonFn(payload) — Brotli Q11
    ML->>ML: extractSearchableFieldsFn(payload)
    ML->>DB: UPDATE {payload:Buffer, search_terms:string}
    deactivate ML

    P->>SL: Forwarded HTTP request
    SL-->>P: HTTP response (code, body)

    P->>ML: emit('logs.legacy.update', {api, hash, response, code, user?})
    activate ML
    ML->>DB: findUnique {hash, finishedAt: null}
    ML->>ML: compressJsonFn(response) — Brotli Q11
    ML->>ML: mergeSearchTermsFn(existing, response)
    ML->>DB: UPDATE {response:Buffer, code, status, finishedAt, duration(s), search_terms}
    deactivate ML

    P-->>U: HTTP response
```

## Extracción de términos de búsqueda

Al momento del `create` y del `update`, se extraen valores de dominio del payload/response:

```mermaid
flowchart LR
    A["Payload JSON\n{cupo: 'C123', grano: 'maiz', ...}"] --> B["extractSearchableFieldsFn()"]
    B --> C["'C123 maiz ...' (string concatenado)"]
    C --> D["search_terms en BD"]
    
    E["Response JSON\n{ctg: '99887766', dominio: 'ABC123'}"] --> F["mergeSearchTermsFn(existing, response)"]
    F --> G["'C123 maiz ... 99887766 ABC123' (fusionado)"]
    G --> D
```

## Ventana de inconsistencia

```
Tiempo →

T0: INSERT (payload=null, status=PENDING)       ← registro existe sin payload
T1: UPDATE payload=Buffer, search_terms=...     ← registro completo (create)
...
T2: INSERT  ← legacy.update recibido
T3: UPDATE response, status, finishedAt...      ← registro finalizado
```

Entre T0 y T1 hay una ventana donde el registro puede ser consultado con `payload = null`. Si la app falla entre T0 y T1, el payload se pierde permanentemente.

## Manejo de usuarios tardíos

Si `legacy.create` se envió sin `user` y `legacy.update` incluye `user`:

```
legacy.update → detecta record.user = null y payload.user != null
             → _upsertUser() se llama con void (background)
             → Si falla, no hay registro del error
```

---

*Ver también: [[flujo-tracing-graphql]] · [[legacy-create]] · [[legacy-update]] · [[entidad-legacy]]*

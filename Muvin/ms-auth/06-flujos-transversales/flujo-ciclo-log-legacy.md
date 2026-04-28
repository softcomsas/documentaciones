# Flujo: Ciclo de Vida de Log Legacy

> **Última revisión:** 2026-04-27
> **Módulos involucrados:** [[modulo-logs]]

---

## Descripción

Flujo completo de registro de trazabilidad para un request del sistema legacy. Usa el campo `hash` como identificador de correlación para unir el registro inicial con su actualización posterior.

---

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant Auth as ms-auth
    participant Logs as ms-logs\n(TCP)
    participant LogDB as BD ms-logs

    Note over Auth,LogDB: Request entrante
    Auth->>Auth: Genera hash de correlación
    Auth->>Logs: emit('logs.legacy.create',\n{api, hash, user?, method, endpoint, payload})
    Logs->>LogDB: INSERT Log {status: PENDING}

    Note over Auth,LogDB: Procesamiento
    Auth->>Auth: Procesa el request\n(valida, consulta BD, etc.)

    Note over Auth,LogDB: Respuesta obtenida
    alt Respuesta exitosa (2xx)
        Auth->>Logs: emit('logs.legacy.update',\n{api, hash, response, code: 200, user?})
        Logs->>LogDB: UPDATE Log {response, code, status: SUCCESS}
    else Error de negocio (4xx)
        Auth->>Logs: emit('logs.legacy.update',\n{api, hash, response, code: 4xx})
        Logs->>LogDB: UPDATE Log {response, code, status: ERROR}
    else Timeout
        Auth->>Logs: emit('logs.legacy.update',\n{api, hash, response: null, code: 408})
        Logs->>LogDB: UPDATE Log {status: TIMEOUT}
    end
```

---

## Consideraciones de implementación

- El `hash` debe generarse de forma única por request (UUID v4 o similar).
- El `emit` es fire-and-forget — si ms-logs está caído, el log se pierde sin afectar el procesamiento del request.
- No existe mecanismo de reintento ni buffer local para logs no entregados.

## Riesgos

- ⚠️ Logs perdidos si ms-logs no está disponible — trazabilidad comprometida.
- ⚠️ Sin idempotencia en `create` — si se emite dos veces el mismo hash, puede haber duplicados.

# Flujo: Autenticación de Request Moderno

> **Última revisión:** 2026-04-27
> **Módulos involucrados:** [[modulo-auth]], [[modulo-logs]]

---

## Descripción

Flujo end-to-end de autenticación de un request proveniente del sistema moderno (API Gateway u otro microservicio). El consumidor debe primero obtener una firma y luego validar sus credenciales antes de acceder a recursos protegidos.

---

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant Consumer as Consumidor\n(API Gateway)
    participant Auth as ms-auth\n(TCP)
    participant DB as MySQL\n(local)

    Note over Consumer,DB: Paso 1 — Generar firma
    Consumer->>Auth: send('auth.generate.signature', {key, timestamp, ...data})
    Auth->>DB: SELECT Key WHERE key = ? AND active = true
    DB-->>Auth: {id, key, secret, company, active}
    Auth->>Auth: HMAC(secret, payload+timestamp) = signature
    Auth-->>Consumer: {success: true, data: {signature}}

    Note over Consumer,DB: Paso 2 — Validar credenciales
    Consumer->>Auth: send('auth.validate.key', {key, signature, timestamp})
    Auth->>DB: SELECT Key WHERE key = ? AND active = true
    DB-->>Auth: {id, key, secret, company, active}
    Auth->>Auth: Verificar timestamp en ventana válida
    Auth->>Auth: Verificar HMAC(secret, ...) == signature
    Auth-->>Consumer: {success: true, data: IAuth}

    Note over Consumer,Auth: Paso 3 — Validar autorización (opcional)
    Consumer->>Auth: send('auth.validate.authorization', {company, ...})
    Auth-->>Consumer: {success: true, data: ...}
```

> [!warning] Flujo esperado según contratos definidos. Handlers no implementados.

---

## Precondiciones

- La compañía tiene al menos una clave activa (`active: true`).
- El timestamp enviado está dentro de la ventana de validez (⚠️ ventana no definida aún).

---

## Postcondiciones

- El consumidor recibe `IAuth` con los datos de la compañía y la clave.
- El consumidor puede proceder con el request protegido.

---

## Flujos alternativos

| Condición | Resultado |
|-----------|-----------|
| Key no existe o `active: false` | Error: clave inválida |
| Timestamp fuera de ventana | Error: request expirado |
| Firma no coincide | Error: firma inválida |
| Compañía sin autorización para la operación | Error: no autorizado |

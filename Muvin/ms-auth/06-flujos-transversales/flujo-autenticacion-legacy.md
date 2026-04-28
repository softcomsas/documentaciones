# Flujo: Autenticación de Request Legacy

> **Última revisión:** 2026-04-27
> **Módulos involucrados:** [[modulo-auth]], [[modulo-logs]]

---

## Descripción

Flujo de autenticación para requests provenientes del sistema legacy (LEGACY_PANEL o LEGACY_DESCARGAS). Utiliza un protocolo de validación diferente al moderno y genera logs de trazabilidad en ms-logs.

---

## Diagrama de secuencia

```mermaid
sequenceDiagram
    participant Legacy as Sistema Legacy\n(LEGACY_PANEL / DESCARGAS)
    participant Auth as ms-auth\n(TCP)
    participant DB as MySQL\n(local)
    participant Logs as ms-logs\n(TCP)

    Legacy->>Auth: send('auth.validate.legacy', {credenciales legacy})

    Auth->>Logs: emit('logs.legacy.create', {api, hash, method, endpoint, payload})
    Note right of Logs: Log queda en PENDING

    Auth->>DB: Validar credenciales según protocolo legacy
    DB-->>Auth: Resultado de validación

    alt Validación exitosa
        Auth->>Logs: emit('logs.legacy.update', {hash, response, code: 200})
        Note right of Logs: Log pasa a SUCCESS
        Auth-->>Legacy: {success: true, data: identidad validada}
    else Validación fallida
        Auth->>Logs: emit('logs.legacy.update', {hash, response, code: 401})
        Note right of Logs: Log pasa a ERROR
        Auth-->>Legacy: {success: false, error: {code: 401, message: ...}}
    end
```

> [!warning] Flujo esperado según contratos. Handlers no implementados. El protocolo de validación legacy es ⚠️ Pendiente de verificar.

---

## Precondiciones

- El sistema legacy envía credenciales en el formato esperado por `auth.validate.legacy`.
- `ms-logs` está disponible para recibir los emits (si no lo está, los logs se pierden).

---

## Postcondiciones

- El sistema legacy recibe respuesta de autenticación.
- El evento queda registrado en ms-logs (si ms-logs está disponible).

---

## Riesgos

- 🔴 El protocolo legacy no está documentado — implementar requiere reverse engineering.
- ⚠️ Los logs se emiten como fire-and-forget — si ms-logs falla, no hay registro del acceso.

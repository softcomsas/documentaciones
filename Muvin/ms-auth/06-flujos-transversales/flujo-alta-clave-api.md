# Flujo: Alta de Clave API

> **Última revisión:** 2026-04-27
> **Módulos involucrados:** [[modulo-auth]], [[modulo-integrations]]

---

## Descripción

Flujo de creación de un nuevo par key/secret para una compañía. El administrador o sistema de onboarding invoca este flujo para habilitar una compañía para autenticarse en el ecosistema.

---

## Diagrama

```mermaid
flowchart TD
    A([Caller envía: auth.create.key\n{company: id}]) --> B[Verificar que la compañía existe]
    B --> C{¿Existe?}
    C -->|No| ERR[Error: compañía no encontrada]
    C -->|Sí| D[Generar key aleatoria\ncryptographically secure]
    D --> E[Generar secret aleatorio\ncryptographically secure]
    E --> F[Hashear secret]
    F --> G[INSERT en tabla Key\n{key, secret_hash, company, active:true}]
    G --> H[Emit: integrations.email.notification\nNotificar a la compañía]
    H --> I[Retornar IKey\n{id, key, secret_plain, company, active}]
    I --> Z([Respuesta al caller])
    ERR --> Z
```

> [!warning] Flujo esperado — handlers no implementados. Los pasos de hashing y notificación son inferidos como buenas prácticas; no están definidos explícitamente en los contratos.

---

## Postcondiciones

- La compañía tiene una nueva clave activa en la BD.
- El caller recibe el `secret` en texto plano **una única vez** (no debe almacenarse en el sistema).

## Riesgos

- 🔴 Si el `secret` no se hashea antes de persistir, queda expuesto en BD.
- 🔒 Si el caller no almacena el `secret` en ese momento, se pierde — no hay endpoint de recuperación en el contrato.
- ⚠️ No hay límite de claves por compañía definido en el contrato.

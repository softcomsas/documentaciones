# Flujos Transversales — Índice

> **Última revisión:** 2026-04-29

Flujos que atraviesan múltiples módulos o definen el comportamiento estándar del sistema.

---

## Flujos documentados

| Flujo | Descripción | Archivo |
|-------|-------------|---------|
| F-TX-01 | Ciclo de vida de un request (AES → JWT → proxy → normalize) | [[flujo-proxy-aes]] |
| F-TX-02 | Flujo de integración Viterra (DB local) | [[flujo-viterra]] |
| F-TX-03 | Ciclo OAuth2 SiloHub (token singleton) | [[flujo-proxy-aes]] |

---

## Diagrama de flujo global

```mermaid
flowchart TD
    subgraph Cliente
        A[App / Panel]
    end

    subgraph api-bus
        B[JWT Middleware\nApiRestMuvinController]
        C[HtmlPurifier\nbeforeRequest]
        D{Módulo}
        E1[silohub:\nAES decrypt → OAuth2 → proxy]
        E2[timbues:\nAES decrypt → token → proxy]
        E3[viterra:\nDB local ActiveRecord]
        E4[monsanto:\nAuth propio → CerealTrack]
        E5[agroquimicos:\nZarcam + SanMiguel GPS]
        F[normalizeResponse\n{success, status, data}]
    end

    A -->|Bearer JWT| B
    B -->|válido| C
    C -->|sanitizado| D
    D --> E1 & E2 & E3 & E4 & E5
    E1 & E2 & E3 & E4 & E5 --> F
    F -->|JSON| A
```

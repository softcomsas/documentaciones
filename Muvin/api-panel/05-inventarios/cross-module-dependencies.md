# Dependencias Entre Módulos — Muvinapp

> **Última revisión:** 2026-04-21
> **Ver también:** [[arquitectura-alto-nivel]], [[depends-matrix]]

---

## Mapa de dependencias

```mermaid
graph LR
    subgraph CORE["Módulos Core"]
        AUTH["🔒 auth/service\n(RBAC)"]
        V1["v1\n(principal)"]
        COMMON["common/\n(models, components)"]
    end

    subgraph BUSINESS["Módulos de negocio"]
        V2["v2 (cupos legacy)"]
        V3["v3 (cupos CCPP)"]
        MAGYP["magyp (AFIP)"]
        TURNEADA["turneada"]
        FERT["fertilizantes"]
        AGRO["agroquimicos"]
        MTR["mtr (MATba)"]
        ERP["erp"]
        BOT["bot"]
    end

    subgraph INFRA["Infraestructura compartida"]
        QUEUE["Queue (jobs)"]
        CACHE["FileCache"]
        LOG["logService (gateway)"]
    end

    %% Todos usan common/
    V1 --> COMMON
    V2 --> COMMON
    V3 --> COMMON
    MAGYP --> COMMON
    TURNEADA --> COMMON
    FERT --> COMMON
    AGRO --> COMMON
    MTR --> COMMON
    ERP --> COMMON
    BOT --> COMMON

    %% Todos dependen de auth
    V1 --> AUTH
    V2 --> AUTH
    V3 --> AUTH
    MAGYP --> AUTH
    TURNEADA --> AUTH
    FERT --> AUTH

    %% Dependencias de negocio
    V3 -->|"usa modelos Cupo/CartaPorte"| V1
    V3 -->|"Carta Porte Electrónica"| MAGYP
    BOT -->|"Crea pedidos/demandas"| V1
    ERP -->|"Consulta cupos/choferes"| V1
    FERT -->|"Usa modelos de centro/cupo"| COMMON
    AGRO -->|"Usa modelos de pedido"| COMMON
    V2 -->|"Modelos cupo legacy"| COMMON

    %% Infra
    V1 --> QUEUE
    MAGYP --> QUEUE
    V1 --> LOG
    V3 --> LOG
    COMMON --> CACHE
```

---

## Dependencias transitivas críticas

| Si falla... | Se ven afectados... | Impacto |
|------------|---------------------|---------|
| `common/` (models) | Todos los módulos | 💀 Sistema caído |
| `auth/service` (RBAC) | Todos los módulos (autenticación) | 💀 Sistema caído |
| `MAGYP` / AFIP | `v3` (carta de porte), `MAGYP` | 🔴 No se pueden emitir cartas de porte |
| `Queue` | Jobs de notificaciones, AFIP | 🔴 Procesamiento asíncrono detenido |
| `Gateway API` (log) | Todos (solo log) | 🟡 Logs perdidos, operación continúa |
| `v1` controllers | `bot`, `erp` (consultas) | 🟡 Funcionalidades parciales afectadas |

---

## Módulos y sus dependencias a componentes common/

| Módulo | Componentes que usa |
|--------|-------------------|
| **v1 / v3** | `IntegracionAfip`, `StopConnect`, `PdfResources`, `GenerarExcel`, `Socket`, `Notificacion`, `AsyncCurl`, `MicroServicioRbac`, `GatewayLog` |
| **magyp** | `HttpClient`, `GatewayLog` |
| **mtr** | `MtrConnect`, `GatewayLog` |
| **bot** | `WhatsApp`, `Infobip`, `Socket` |
| **turneada** | `Notificacion`, modelos `Turneada*` |
| **fertilizantes** | Modelos `Cupo*`, `Centro*` |
| **erp** | Modelos varios |
| **queue jobs** | `IntegracionAfip`, `Notificacion`, `Socket` |

---

## Notas

- No se detectó uso de interfaces/contratos explícitos entre módulos — la comunicación es por instanciación directa de clases y consultas ActiveRecord.
- Los módulos NO se llaman entre sí directamente vía HTTP interno — comparten modelos de `common/`.
- El Bus de Integración (`BusIntegracion`) es el único mecanismo de comunicación asíncrona hacia sistemas externos (VTerra).

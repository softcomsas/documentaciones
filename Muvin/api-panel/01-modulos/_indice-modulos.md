# Índice de Módulos — Muvinapp

> **Última revisión:** 2026-04-21
> **Ver también:** [[arquitectura-alto-nivel]], [[functional-classification]]

---

## Módulos versionados

| Módulo | Estado | Doc |
|--------|--------|-----|
| [[modulo-v2\|V2 (Legacy)]] | 🟡 Legacy — mantener sin evolucionar | [[modulo-v2]] |
| [[modulo-v3\|V3 (Actual)]] | 🟢 Principal — en uso activo | [[modulo-v3]] |

## Módulos de integración externa

| Módulo | Integra con | Doc |
|--------|-------------|-----|
| [[modulo-magyp\|MAGYP]] | AFIP / Ministerio Agricultura | [[modulo-magyp]] |
| [[modulo-mtr\|MTR]] | MATba / Rofex | [[modulo-mtr]] |

## Módulos de negocio principal

| Módulo | Propósito | Doc |
|--------|-----------|-----|
| [[modulo-cupos\|Cupos]] | Ciclo de vida de cupos de descarga | [[modulo-cupos]] |
| [[modulo-choferes\|Choferes]] | Gestión de conductores | [[modulo-choferes]] |
| [[modulo-centros\|Centros]] | Terminales y plantas de descarga | [[modulo-centros]] |
| [[modulo-viajes\|Viajes]] | Solicitudes y seguimiento de viajes | [[modulo-viajes]] |
| [[modulo-turneada\|Turneada]] | Sistema de turnos de descarga | [[modulo-turneada]] |
| [[modulo-auth\|Auth/RBAC]] | Autenticación y permisos | [[modulo-auth]] |

## Módulos especializados

| Módulo | Propósito | Doc |
|--------|-----------|-----|
| [[modulo-fertilizantes\|Fertilizantes]] | Cupos de fertilizantes | [[modulo-fertilizantes]] |
| [[modulo-agroquimicos\|Agroquímicos]] | Pedidos de agroquímicos | [[modulo-agroquimicos]] |
| [[modulo-erp\|ERP]] | Integración con ERP externo | [[modulo-erp]] |
| [[modulo-bot\|Bot]] | ChatBot WhatsApp para choferes | [[modulo-bot]] |

---

## Diagrama de módulos

```mermaid
graph TD
    CORE["🏗️ Core — common/\nModelos + Componentes"]
    AUTH["🔒 auth\n(JWT + RBAC)"]
    V1["📦 v1 (principal)\n~170 controladores"]
    V2["📦 v2 (legacy)\nCupoController"]
    V3["📦 v3 (actual)\nCupo + CCPP + Turnos"]
    MAGYP["🔌 magyp\nAFIP Carta de Porte"]
    MTR["🔌 mtr\nMATba/Rofex"]
    FERT["📦 fertilizantes\nCupos + Novedades"]
    AGRO["📦 agroquimicos\nPedidos"]
    ERP["📦 erp\nIntegración ERP"]
    BOT["📦 bot\nChatBot WhatsApp"]
    TURNEADA["📦 turneada\nTurnos de descarga"]

    V1 --> CORE
    V2 --> CORE
    V3 --> CORE
    V3 --> MAGYP
    MAGYP --> CORE
    MTR --> CORE
    FERT --> CORE
    AGRO --> CORE
    ERP --> CORE
    BOT --> CORE
    BOT --> V1
    ERP --> V1
    TURNEADA --> CORE
    ALL_MODS[" "] --> AUTH

    style ALL_MODS height:0px,width:0px
```

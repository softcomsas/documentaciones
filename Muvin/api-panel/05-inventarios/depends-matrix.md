# Matriz de Dependencias Entre Módulos — Muvinapp

> **Última revisión:** 2026-04-21
> **Ver también:** [[cross-module-dependencies]], [[arquitectura-alto-nivel]]

---

## Convenciones

| Símbolo | Significado |
|---------|-------------|
| `🔴` | Dependencia directa — falla si el otro falla |
| `🟡` | Dependencia parcial — funcionalidad reducida |
| `⬜` | Sin dependencia directa conocida |
| `🔵` | Dependencia vía shared `common/` |

---

## Matriz (filas = depende de → columnas)

|           | **common/** | **auth/svc** | **v1** | **v2** | **v3** | **magyp** | **mtr** | **turneada** | **fertiliz.** | **agroquim.** | **erp** | **bot** |
|-----------|------------|-------------|--------|--------|--------|-----------|---------|-------------|--------------|--------------|---------|---------|
| **v1**       | 🔴          | 🔴           | —      | ⬜     | ⬜     | ⬜        | ⬜      | ⬜          | 🟡           | 🟡           | ⬜      | ⬜      |
| **v2**       | 🔴          | 🔴           | 🟡     | —      | ⬜     | ⬜        | ⬜      | ⬜          | ⬜           | ⬜           | ⬜      | ⬜      |
| **v3**       | 🔴          | 🔴           | 🟡     | ⬜     | —      | 🔴        | ⬜      | ⬜          | ⬜           | ⬜           | ⬜      | ⬜      |
| **magyp**    | 🔴          | 🔴           | ⬜     | ⬜     | 🟡     | —         | ⬜      | ⬜          | ⬜           | ⬜           | ⬜      | ⬜      |
| **mtr**      | 🔴          | 🔴           | ⬜     | ⬜     | ⬜     | ⬜        | —       | ⬜          | ⬜           | ⬜           | ⬜      | ⬜      |
| **turneada** | 🔴          | 🔴           | 🟡     | ⬜     | ⬜     | ⬜        | ⬜      | —           | ⬜           | ⬜           | ⬜      | ⬜      |
| **fertiliz.**| 🔴          | 🔴           | 🟡     | ⬜     | ⬜     | ⬜        | ⬜      | ⬜          | —            | ⬜           | ⬜      | ⬜      |
| **agroquim.**| 🔴          | 🔴           | 🟡     | ⬜     | ⬜     | ⬜        | ⬜      | ⬜          | ⬜           | —            | ⬜      | ⬜      |
| **erp**      | 🔴          | 🔴           | 🟡     | ⬜     | ⬜     | ⬜        | ⬜      | ⬜          | ⬜           | ⬜           | —       | ⬜      |
| **bot**      | 🔴          | 🔴           | 🔴     | ⬜     | ⬜     | ⬜        | ⬜      | ⬜          | ⬜           | ⬜           | ⬜      | —       |

---

## Nivel de acoplamiento por módulo

| Módulo | Dependencias entrantes (usado por) | Dependencias salientes (usa a) | Acoplamiento |
|--------|-----------------------------------|-------------------------------|-------------|
| **common/** | Todos los módulos | Externo: BD, Cache, Servicios | 🔴 Acoplamiento máximo (hub) |
| **auth/service** | Todos los módulos | RBAC externo, common/ | 🔴 Alto |
| **v1** | v2, v3, bot, erp, turneada | common/, auth, fertilizantes, agroquimicos | 🔴 Alto |
| **v3** | Ninguno directo | v1, magyp, common/, auth | 🟡 Medio |
| **magyp** | v3 | common/, auth, AFIP externo | 🟡 Medio |
| **mtr** | Ninguno | common/, auth, MATba externo | 🟢 Bajo |
| **turneada** | Ninguno | v1, common/, auth | 🟡 Bajo-Medio |
| **fertilizantes** | Ninguno | v1, common/, auth | 🟡 Bajo-Medio |
| **agroquimicos** | Ninguno | v1, common/, auth | 🟢 Bajo |
| **erp** | Ninguno | v1, common/, auth | 🟢 Bajo |
| **bot** | Ninguno | v1, common/, auth, WhatsApp/Infobip | 🟡 Medio |
| **v2** | Ninguno | v1, common/, auth | 🟡 Bajo-Medio |

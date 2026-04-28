# Hotspots — Zonas de Riesgo

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21

Un hotspot es un archivo o módulo que concentra complejidad, lógica crítica o riesgos de forma desproporcionada. Cambios en estos archivos tienen alto impacto.

---

## 🔴 Hotspot crítico: `src/modules/gmail/service.ts`

**Riesgo:** CRÍTICO | **Líneas:** ~300 | **Responsabilidades:** 6+

Este archivo es el núcleo del microservicio. Concentra:

1. Bootstrap / inicialización del servicio (`onApplicationBootstrap`)
2. Gestión de token de credencial (`_setToken`)
3. Gestión de sesiones por cuenta (`_setUpSessions`)
4. Sync de watches de Gmail API (`_syncWatches`)
5. Procesamiento de notificaciones (`notification`)
6. Integración con Bull queue (`queue.add`)
7. Integración con Gmail API (history.list, messages.get)

**Problemas identificados:**

| Problema | Severidad |
|---|---|
| Clave privada en payload de job Bull | 🔴 CRÍTICO |
| Sin manejo de errores en `notification()` (retorna void) | 🔴 CRÍTICO |
| Todo el estado del servicio en propiedades privadas en memoria | 🟡 MEDIO |
| `_syncLabels()` comentada — labels no se sincronizan con Gmail | 🟡 MEDIO |
| Sesiones cargadas una vez, sin refresh si cambian cuentas en DB | 🟡 MEDIO |

**Regla práctica:** Cualquier cambio en este archivo debe testearse exhaustivamente y revisarse por al menos 2 personas. Ver [[security-inventory]].

---

## 🟡 Hotspot: `src/core/repositories/gmail-labels.ts`

**Riesgo:** MEDIO | **Responsabilidad:** Cache estática de labels

Carga todos los labels de la DB al bootstrap y los mantiene en una lista inmutable en memoria. Problemas:

- Cambios en `gmail_labels` en DB no se reflejan hasta reinicio.
- Si la DB está caída en bootstrap, el servicio arranca sin labels y silenciosamente ignora todos los mensajes.
- No hay mecanismo de refresh programático.

---

## 🟡 Hotspot: `src/core/repositories/gmail-credentials.ts`

**Riesgo:** MEDIO | **Responsabilidad:** Credencial única del sistema

Implementa `findFirstDomain()` sin criterio de selección — toma siempre el primer registro. Problemas:

- No escala a múltiples dominios/credenciales.
- Sin rotación de credenciales.
- Si el primer registro tiene datos incorrectos, el servicio falla completamente.

---

## 🟢 Bajo riesgo: Resto de repositorios

Los repositorios `GmailAccountsRepository`, `GmailMessagesRepository` son simples wrappers de Prisma con operaciones acotadas. Bajo riesgo.

---

## Ver también

- [[security-inventory]]
- [[deuda-tecnica]]
- [[modulo-gmail]]

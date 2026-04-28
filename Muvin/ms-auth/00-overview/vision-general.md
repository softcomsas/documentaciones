# Visión General

> **Proyecto:** muvin-ms-auth
> **Última revisión:** 2026-04-27

---

## ¿Qué hace el sistema?

`ms-auth` es el **microservicio de autenticación y autorización** del ecosistema Muvin (BCR). Es responsable de:

1. **Gestionar claves API** (pares key/secret) asignadas a compañías registradas.
2. **Validar la identidad** de consumidores que acceden al ecosistema, tanto mediante el protocolo moderno (firma criptográfica + timestamp) como mediante el protocolo del sistema legacy.
3. **Definir los contratos tipados** de comunicación con otros tres microservicios del ecosistema: `ms-commercial`, `ms-logs` y `ms-integrations`.

---

## ¿A quién sirve?

| Actor | Rol |
|-------|-----|
| API Gateway / ms-panel | Consumidor principal — valida requests de usuarios antes de procesarlos |
| Sistema Legacy (LEGACY_PANEL, LEGACY_DESCARGAS) | Consumidor legacy — autentica mediante protocolo heredado |
| ms-commercial | Microservicio externo — recibe comandos de ms-auth para gestión de contratos |
| ms-logs | Microservicio externo — recibe eventos de log emitidos por ms-auth |
| ms-integrations | Microservicio externo — recibe solicitudes de notificación por email |

---

## Estado actual

| Dimensión | Estado |
|-----------|--------|
| Infraestructura y configuración | ✅ Completa |
| Contratos tipados (interfaces RPC) | ✅ Completos |
| PrismaService (acceso a BD) | ✅ Implementado |
| Handlers RPC (lógica de negocio) | 🚧 No implementados |
| Repositorios de datos | 🚧 No implementados |
| Tests | ❌ Ninguno |
| Migraciones de BD | ❌ No versionadas |

> [!warning] El microservicio está en estado de scaffolding avanzado. Tiene una arquitectura bien diseñada y contratos tipados completos, pero aún no procesa ningún mensaje TCP. No debe desplegarse en producción hasta implementar los handlers.

---

## Métricas del proyecto

| Métrica | Valor |
|---------|-------|
| Módulos de contratos | 4 (auth, commercial, logs, integrations) |
| Comandos RPC definidos | 19 |
| Comandos RPC implementados | 0 |
| Archivos TypeScript fuente | ~40 |
| Líneas de código (aprox.) | ~500 LOC |
| Tests existentes | 0 |
| Cobertura de tests | 0% |

---

## Historia del proyecto

`ms-auth` es parte del ecosistema Muvin, desarrollado para BCR. Es un proyecto relativamente reciente en términos de stack tecnológico (NestJS 11, TypeScript 5, Prisma 6), pero convive con un sistema legacy activo al que debe dar soporte de autenticación.

---

## Referencias

- [[arquitectura-alto-nivel]] — Diagrama de capas y componentes
- [[stack-tecnologico]] — Versiones y estado del stack
- [[deuda-tecnica]] — Trabajo pendiente priorizado
- [[security-inventory]] — Hallazgos de seguridad

---
tags: [hotspots, riesgos, complejidad, cambios]
última-revisión: 2026-04-27
---

# Hotspots de riesgo

> **Estado actual:** Proyecto en estado inicial. No hay código de dominio con complejidad acumulada ni histórico de bugs.

## Archivos de mayor criticidad actual

| Archivo | Razón de criticidad | Riesgo actual |
|---------|--------------------|--------------:|
| `src/config/environments.ts` | Punto de falla único al arranque. Si la validación Joi falla, el MS no inicia. | 🟡 |
| `src/common/cmd/constant.ts` | Fuente única de verdad de todos los message patterns. Un error aquí rompe la comunicación inter-microservicios silenciosamente. | 🟡 |
| `src/contracts/system.ts` | Define los tipos de contrato base (`TContractSend`, `TContractEmit`). Cambios aquí afectan todos los contratos del ecosistema. | 🟡 |
| `prisma/schema.prisma` | Vacío actualmente. Cuando tenga modelos, será el archivo más crítico — cada cambio requiere migración. | 🔴 (cuando se use) |

## Hotspots esperados al implementar features

A medida que se desarrollen módulos de dominio, vigilar especialmente:

| Patrón de riesgo | Descripción | Mitigación |
|-----------------|-------------|-----------|
| Funciones handler >50 líneas | Los `@MessagePattern` handlers tienden a crecer | Extraer lógica a servicios |
| Transacciones Prisma | Operaciones multi-tabla sin `$transaction()` | Usar siempre `prisma.$transaction()` para escrituras múltiples |
| Timeout en `send()` | Los mensajes TCP pueden no recibir respuesta si el servicio externo está caído | Configurar timeout + manejo de errores en cada `send()` |
| Falta de reintentos en `send()` | Una falla transitoria de red mata la operación | Implementar retry con backoff para operaciones críticas |
| Tipos `any` en contratos | Usar `unknown` o tipos específicos en payloads de mensajes | Respetar los tipos definidos en `src/contracts/` |

## Complejidad ciclomática esperada por componente

| Componente | Complejidad esperada | Umbral de alerta |
|------------|---------------------|:----------------:|
| Handlers `@MessagePattern` | Baja–Media | >10 ramas |
| Servicios de dominio | Media | >15 ramas |
| Repositorios | Baja | >5 ramas |
| Funciones utilitarias | Muy baja | >3 ramas |

# Visión General — App Agronomy (muvin-lite)

> **Última revisión:** 2026-04-30

## ¿Qué es este sistema?

`muvin-lite` es una aplicación web SPA/PWA desarrollada en Angular 16 orientada a **operadores de centros agroindustriales** (rol `3`), **dadores de carga** (rol `5`) y otros roles auxiliares (rol `11`, `16`). Permite operar el ciclo logístico de granos y fertilizantes: desde la creación de solicitudes de pedido hasta la asignación de vehículos y choferes para el despacho.

## Contexto de negocio

La aplicación se sitúa en el ecosistema **Muvin**, un conjunto de servicios que gestiona operaciones de acopio, transporte y comercialización de granos en el agro argentino. Esta app actúa como **portal de centros y transportistas**: consume la misma API REST que el panel principal (Muvin AISA) pero con una interfaz simplificada y optimizada para uso en dispositivos móviles (PWA).

## Estado actual

| Ítem | Detalle |
|------|---------|
| Estado | Producción / Mantenimiento activo |
| Versión Angular | 16.2.x |
| PWA | Sí (`@angular/service-worker`, `manifest.webmanifest`) |
| i18n | Sí (`@ngx-translate/core`) |
| Módulos funcionales | 3 (Sessions, Pedidos, Transportistas) |
| Módelos de dominio | ~90 interfaces TypeScript en `shared/models/` |

## Usuarios objetivo

| Rol | ID | Descripción |
|-----|----|-------------|
| Centro | `3` | Operador del centro agroindustrial. Gestiona pedidos y cupos |
| Dador de carga | `5` | Empresa que origina la carga. Ve sus propios pedidos |
| Rol auxiliar 1 | `11` | ⚠️ Pendiente de verificar descripción exacta |
| Rol auxiliar 2 | `16` | ⚠️ Pendiente de verificar descripción exacta |

## Números relevantes

- **~90** interfaces de dominio en `src/app/shared/models/`
- **17** componentes en el módulo Pedidos
- **7** componentes en el módulo Sessions
- **5** componentes en el módulo Transportistas
- **~24** servicios en `shared/services/`
- **5** servicios específicos en el módulo Pedidos
- **6** servicios específicos en el módulo Transportistas

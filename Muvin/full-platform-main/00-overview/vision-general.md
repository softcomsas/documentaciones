# Visión General — Plataforma AISA

> **Estado:** Producción activa
> **Última revisión:** 2026-04-29

## ¿Qué hace el sistema?

La **Plataforma AISA** (`full-platform-main`) es un monorepo Nx que concentra todos los microfrontends y microservicios del ecosistema digital de AISA. Su dominio principal es la **operación de granos**: gestión de cupos de descarga, carta porte (camión y tren), logística de viajes, ofertas comerciales, documentación empresarial y pedidos.

## ¿A quién sirve?

- **Operadores de planta / plataforma:** carga cupos, gestiona descargas, carta porte y adendas.
- **Área logística:** seguimiento de viajes, cargas, choferes y equipos.
- **Área comercial:** publicación y gestión de ofertas de granos.
- **Proveedores y entidades externas:** carga y consulta de documentación.
- **Administradores del sistema:** gestión de roles, usuarios y configuraciones.

## Historia breve

El sistema comenzó como aplicaciones Yii2 PHP independientes (logística, oferta, documentación). Posteriormente se inició una migración hacia una arquitectura Angular + NestJS con Nx y Module Federation, conviviendo actualmente ambas generaciones tecnológicas en el mismo monorepo.

## Estado actual

| Componente | Estado |
|---|---|
| Main shell Angular 16 | ✅ Producción |
| Auth App (MFE) | ✅ Producción |
| Descargas App (MFE + NestJS) | ✅ Producción |
| Logística App (MFE + Yii2) | ✅ Producción |
| Oferta App (MFE + Yii2) | ✅ Producción |
| Documentación App (MFE + Yii2) | ✅ Producción |
| Pedidos App (NestJS + Ionic) | 🚧 En desarrollo / parcial |
| SuperApp (Angular + Ionic + NestJS) | 🚧 En desarrollo / parcial |

## Números relevantes

| Métrica | Valor aproximado |
|---|---|
| Aplicaciones en el monorepo | 9 apps (frontend + backend) |
| Microfrontends Angular | 7 |
| Backends NestJS | 3 (descargas, pedidos, superapp) |
| Backends Yii2 PHP (legacy) | 3 (logística, oferta, documentación) |
| Apps mobile Ionic | 2 (pedidos-mobile, superapp-mobile) |
| Módulos NestJS en descargas | 7 (descargas, reportes, cron, txt-upload, transferencia, adenda, cartaPorteTren) |
| Controladores Yii2 (logística) | 15 |
| Controladores Yii2 (documentación) | 14 |
| Versión Node requerida | v18.16.0 |
| Gestor de paquetes | pnpm v8.8.0 |

## Relaciones con sistemas externos

- **AFIP:** integración para actualización de cupos de descarga (API REST).
- **APIs externas de granos:** consumidas desde `external.api.service.ts` en descargas-app.
- **Terminales y granos:** datos de referencia consumidos como estáticos o vía API.

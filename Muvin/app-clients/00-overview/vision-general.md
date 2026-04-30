# Visión General — App Clients (muvinapp_clientes)

> **Última revisión:** 2026-04-30

## ¿Qué es este sistema?

`muvinapp_clientes` es una aplicación móvil nativa (Android + iOS) desarrollada en **Flutter/Dart**. Está orientada a **clientes finales, dadores de cupo y administradores de cupos** del ecosistema agroindustrial Muvin. Permite operar cupos de granos, crear solicitudes de carga y gestionar pedidos directamente desde el celular.

## Contexto de negocio

Complementa la plataforma web Muvin. Mientras que `app-agronomy` está orientada a operadores de centros, esta app está pensada para el **cliente/dador** que necesita gestionar sus cupos y cargas en campo o en movimiento.

## Estado actual

| Ítem | Detalle |
|------|---------|
| Estado | Producción / Mantenimiento |
| Versión Flutter | ~2.x (SDK constraint `>=2.1.0 <3.0.0`) |
| Versión app | `1.0.0+2` |
| Plataformas | Android (principal), iOS |
| Arquitectura de estado | BLoC + RxDart |
| Push notifications | Firebase Cloud Messaging |
| Analytics | Firebase Analytics |
| Comunicación en tiempo real | Socket.IO |

## Perfiles de usuario

| Perfil | Condición | Acceso |
|--------|-----------|--------|
| **Cliente final** | `esClienteFinal == '1'` | Gestión Cupos + Gestión Cargas |
| **Dador de cupo** | `esDadorCupo == '1'` | Gestión Cupos + Gestión Cargas |
| **Destinatario** | Rol ID `7` | Acceso diferenciado (esDestino) |
| **Administrador de cupos** | Ninguna de las anteriores | Solo listado de cupos (`v3/cupo/listado`) |

## Números relevantes

- **9 BLoCs** de gestión de estado
- **~30 endpoints** REST documentados en `MuvinProvider`
- **3 módulos de páginas** principales (Auth, Cupos, Cargas)
- **4 proveedores** de servicios (HTTP, Firebase, Socket, Geolocalización)
- **4 modelos** de dominio en `src/models/`

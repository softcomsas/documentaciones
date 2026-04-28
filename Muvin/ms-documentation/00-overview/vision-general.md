---
tags: [visión, overview, propósito]
última-revisión: 2026-04-27
---

# Visión general

## Qué hace el sistema

`muvin-ms-cpe` es un microservicio backend que forma parte del ecosistema **Muvin / BCR**. Su función es actuar como nodo de comunicación TCP dentro de la arquitectura de microservicios, consumiendo servicios de autenticación, logging, comerciales e integraciones externas.

## A quién sirve

- Otros microservicios del ecosistema Muvin que necesiten la funcionalidad que este MS provea.
- El equipo de desarrollo de BCR / Muvin que construye y mantiene el ecosistema.

## Estado actual

| Aspecto | Estado |
|---------|--------|
| Infraestructura base | ✅ Completa |
| Esquema de base de datos | 🚧 Vacío — pendiente de definir |
| Módulos de dominio | 🚧 No implementados |
| Handlers de mensajes TCP | 🚧 No implementados |
| Tests | 🚧 No existen |
| Documentación | ✅ Esta misma |

## Números del proyecto

| Métrica | Valor |
|---------|-------|
| Versión | 0.0.1 |
| Archivos fuente TypeScript | ~30 |
| Módulos NestJS implementados | 2 (AppModule, CoreModule) |
| Módulos de dominio | 0 |
| Endpoints HTTP | 0 |
| Message pattern handlers | 0 |
| Contratos TCP definidos | 21 (en `src/contracts/`) |
| Dependencias de producción | 9 |

## Historia breve

Proyecto de nueva creación dentro del ecosistema Muvin / BCR. La base estructural fue establecida con el stack moderno (NestJS v11, Prisma v6, TypeScript v5.9). Los contratos TCP y las utilidades compartidas están definidos, listos para que los módulos de dominio sean implementados sobre ellos.

## Ver también

- [[stack-tecnologico]]
- [[arquitectura-alto-nivel]]
- [[deuda-tecnica]]

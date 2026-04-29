# Visión General — muvin-app

## ¿Qué es?

`muvin-app` es la nueva aplicación web de Muvin construida sobre **Angular 21** con arquitectura moderna: **Standalone Components**, **Signals** y lazy-loading por defecto en todas las rutas. Gestiona las operaciones sobre cupos de descarga de granos: visualización semanal, asignación y aprobaciones.

## Contexto en el ecosistema Muvin

```
api-plugin (PHP/Yii2)
    └─ descarga cupos de terminales
           ↓
    ms-legacy / ms-commercial  ←──────── muvin-app (Angular 21)
    (NestJS — almacena cupos)            (SPA — visualiza y asigna)
           ↓
    app-panel (Angular 6 — legado)       ← siendo reemplazado
```

## Objetivo de negocio

- Mostrar cupos disponibles y solicitudes de carga en una **grilla semanal** agrupada por producto/zona
- Permitir la **asignación manual** de cupos a solicitudes
- Habilitar la **asignación automática** por reglas
- Proveer filtros avanzados para segmentar por producto, zona, destino, corredor, cliente

## Estado actual

| Aspecto | Estado |
|---|---|
| Módulo `assignment` | ✅ En producción |
| Módulo `quotas` | 🔴 Rutas comentadas — en desarrollo |
| Módulo `requests` | 🔴 Rutas comentadas — en desarrollo |
| Auth / Login | 🔴 Comentado — usa token externo |
| i18n (ES/EN) | ✅ Configurado con `@angular/localize` |

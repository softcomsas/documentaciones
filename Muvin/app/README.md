# muvin-app — Documentación Técnica

> **Proyecto:** muvin-app — Aplicación web de operaciones de cupos
> **Stack:** Angular 21 · TypeScript 5.9 · Angular Material 21 · Standalone Components · Signals
> **Tipo:** SPA standalone para gestión y asignación de cupos de descarga de granos
> **Última revisión:** 2026-04-29

---

> [!abstract] Propósito del sistema
> `muvin-app` es la nueva generación de la aplicación web de Muvin, construida en Angular 21 con arquitectura standalone y Signals. Centraliza las **operaciones sobre cupos de descarga**: visualización semanal de cupos y solicitudes, asignación manual/automática, filtros avanzados y gestión de aprobaciones. Reemplaza progresivamente al `app-panel` legado (Angular 6).

---

## Secciones de documentación

| Sección | Descripción |
|---|---|
| [00-overview/](00-overview/) | Visión general, stack, arquitectura, glosario |
| [01-modulos/](01-modulos/) | Módulos y dominios de la aplicación |
| [02-funcionalidades/](02-funcionalidades/) | Especificaciones funcionales por pantalla |
| [03-servicios/](03-servicios/) | ApiService, ConfigService, interceptores |
| [04-modelo-de-datos/](04-modelo-de-datos/) | Tipos, interfaces y contratos de API |
| [05-inventarios/](05-inventarios/) | Árbol de archivos, componentes, endpoints |
| [06-flujos-transversales/](06-flujos-transversales/) | Flujos de usuario end-to-end |
| [07-operacion/](07-operacion/) | Docker, Nginx, despliegue |
| [08-riesgos/](08-riesgos/) | Deuda técnica, riesgos, mejoras propuestas |

---

## Módulos activos

| Módulo | Ruta | Estado |
|---|---|---|
| `quota-operations` | `/quota-operations` | ✅ Activo |
| `assignment` | `/quota-operations/assignment` | ✅ Activo |
| `quotas` | `/quota-operations/quotas` | 🔴 Pendiente (rutas comentadas) |
| `requests` | `/quota-operations/requests` | 🔴 Pendiente (rutas comentadas) |

---

## Arranque rápido

```bash
npm install
npm start        # ng serve
npm run build    # ng build
npm run i18n     # extraer strings i18n
```

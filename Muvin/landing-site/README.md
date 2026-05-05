# Landing Site — Muvin

> **Stack:** WordPress 5.3.2 · MySQL 5.7 · Nginx 1.15.12 · Docker Compose
> **Dominio:** muvinapp.com
> **Última revisión:** 2026-05-05
> **Repositorio origen:** `landing-site-main/landing-site`

---

> [!info] Propósito
> Sitio web de landing page pública de **Muvin** (muvinapp.com), basado en WordPress con CMS. Sirve como punto de entrada comercial/informativo para usuarios finales. Se despliega completamente en Docker Compose con tres servicios: base de datos MySQL, WordPress FPM y Nginx como proxy/webserver.

---

## 📦 Módulos / Servicios principales

| # | Módulo | Descripción breve | Criticidad | Enlace |
|---|--------|-------------------|------------|--------|
| 1 | WordPress CMS | Motor del sitio, gestión de contenido | 🔴 Alta | [[modulo-wordpress]] |
| 2 | Base de datos MySQL | Almacenamiento de contenido y configuración WP | 🔴 Alta | [[modulo-mysql]] |
| 3 | Nginx Webserver | Proxy inverso, seguridad perimetral, servicio de estáticos | 🟡 Media | [[modulo-nginx]] |
| 4 | Docker Compose | Orquestación de los tres servicios | 🟡 Media | [[modulo-docker-compose]] |

---

## 🔗 Enlaces rápidos a inventarios

- [[tree-estructura-archivos]] — Árbol de archivos del proyecto
- [[cross-module-dependencies]] — Dependencias entre módulos
- [[depends-matrix]] — Matriz de dependencias NxN
- [[functional-classification]] — Clasificación funcional de módulos
- [[core-vs-custom-dependencies]] — Core vs customizaciones
- [[security-inventory]] — Inventario de seguridad 🔒
- [[data-files-index]] — Índice de archivos de datos
- [[reports-and-wizards-inventory]] — Reportes y asistentes

---

## 🗺️ Arquitectura de alto nivel

```mermaid
graph TD
    Internet([Internet / Usuario]) -->|HTTPS:8088| Nginx[Nginx 1.15.12\nwebserver]
    Nginx -->|FastCGI :9000| WP[WordPress 5.3.2-fpm\nwordpress]
    WP -->|MySQL :3306| DB[(MySQL 5.7\ndb)]
    Nginx -->|Archivos estáticos| Vol1[/opt/landingpage/wordpress]
    WP --> Vol1
    DB --> Vol2[/opt/landingpage/dbdata]

    subgraph Docker Network: app-network
        Nginx
        WP
        DB
    end
```

---

## 🧭 Cómo navegar esta documentación

| Ícono | Significado |
|-------|-------------|
| 🟢 | Sano / Bajo riesgo |
| 🟡 | Atención / Riesgo medio |
| 🔴 | Crítico / Alto riesgo |
| ⚠️ | Advertencia puntual |
| 🚧 | Sin verificar / pendiente |
| 💀 | Código muerto / sin uso |
| 🔒 | Afecta seguridad |
| 📦 | Dependencia externa |

Los enlaces `[[nombre-archivo]]` son navegables en **Obsidian**.

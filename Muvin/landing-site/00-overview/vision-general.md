# Visión General — Landing Site Muvin

> **Estado:** Producción
> **Dominio:** muvinapp.com
> **Antigüedad aproximada:** ~2021 (referencias internas en nginx.conf `MP20210228`, `15/04/2021`)

## ¿Qué hace el sistema?

El **Landing Site de Muvin** es el sitio web público institucional/comercial de la plataforma Muvin (`muvinapp.com`). Su función principal es la presentación de la propuesta de valor del producto a usuarios finales potenciales (conductores, empresas de transporte, clientes). Actúa como puerta de entrada al ecosistema Muvin.

Está construido sobre **WordPress** con base de datos **MySQL**, servido mediante **Nginx** como proxy inverso con endurecimiento de seguridad, todo contenedorizado con **Docker Compose**.

## ¿A quién sirve?

- **Usuarios finales anónimos:** visitantes que acceden a muvinapp.com para conocer el producto.
- **Equipo de marketing/contenido:** gestiona el contenido desde el panel de administración de WordPress (`/wp-admin`).
- **Equipo de operaciones/infraestructura:** responsable del despliegue y mantenimiento del stack Docker.

## Historia breve

El sitio fue securizado y configurado entre febrero y abril de 2021 (según comentarios en `nginx.conf`: `MP20210228`, `ADD MP 15/04/2021`). Utiliza el plugin `all-in-one-wp-migration` (v6.77) para backups/migraciones.

## Estado actual

- **Entorno:** Producción
- **Servicios activos:** 3 (db, wordpress, webserver)
- **Versiones:** WordPress 5.3.2, MySQL 5.7, Nginx 1.15.12 — ⚠️ todas con EOL o cerca de EOL
- **Backups:** plugin `all-in-one-wp-migration` v6.77 (archivo `.wpress`)

## Números relevantes

| Métrica | Valor |
|---------|-------|
| Servicios Docker | 3 |
| Archivos de configuración versionados | 4 (docker-compose.yml, nginx.conf, nginx.conf.new, allowip.ip) |
| Módulos funcionales identificados | 4 |
| Versión WordPress | 5.3.2 (⚠️ desactualizada) |
| Versión MySQL | 5.7 (🔴 EOL) |
| Puerto externo expuesto | 8088 |

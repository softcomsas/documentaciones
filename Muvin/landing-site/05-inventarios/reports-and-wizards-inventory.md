# Inventario de Reportes y Asistentes — Landing Site Muvin

> [!info]
> El landing site es un CMS WordPress sin reportes personalizados identificados en el código versionado. No hay archivos `.rdf`, `.jasper`, ni scripts de reporting custom en el repositorio. Los "asistentes" son los wizards nativos de WordPress para gestión de contenido.

## Reportes identificados

| Nombre | Propósito | Archivos origen | Datos consumidos | Módulo | Estado |
|--------|-----------|-----------------|-----------------|--------|--------|
| ⚠️ Pendiente de verificar | No hay reportes custom en el repo | — | — | — | — |

## Asistentes (Wizards) identificados

| Nombre | Propósito | Ruta | Módulo | Estado |
|--------|-----------|------|--------|--------|
| WordPress Setup Wizard | Configuración inicial de WP en primer arranque | `/wp-admin/install.php` | [[modulo-wordpress]] | 💀 Solo en instalación inicial |
| Importador all-in-one-wp-migration | Restaurar un backup `.wpress` | `/wp-admin` → plugin | [[modulo-wordpress]] | 🟢 Activo |
| Exportador all-in-one-wp-migration | Generar backup `.wpress` del sitio | `/wp-admin` → plugin | [[modulo-wordpress]] | 🟢 Activo |

## Notas

- Para un inventario completo de plugins instalados (que podrían incluir reportes, formularios o asistentes adicionales), es necesario inspeccionar el directorio `/opt/landingpage/wordpress/wp-content/plugins/` en el servidor de producción. ⚠️ Pendiente de verificar.

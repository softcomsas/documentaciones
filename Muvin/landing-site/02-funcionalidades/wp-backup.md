# Funcionalidad: Backup y Migración (all-in-one-wp-migration)

> **Módulo:** [[modulo-wordpress]]
> **Plugin:** all-in-one-wp-migration v6.77
> **Archivo de backup:** `all-in-one-wp-migration.6.77.zip`
> **Tipo:** Utilitario / Backup

## Descripción funcional

El plugin `all-in-one-wp-migration` permite exportar todo el sitio WordPress (base de datos + archivos) en un único archivo `.wpress`, y restaurarlo en otro entorno. Está disponible desde `/wp-admin`. El acceso a los archivos de backup generados está restringido en Nginx a IPs internas.

## Ruta de backup generada (protegida en Nginx)

```
/wp-content/ai1wm-backups/*.wpress
```

**Restricción Nginx activa:**
```nginx
location ~* /(?:ai1wm-backups)/.*.wpress$ {
    allow 172.16.0.0/24;
    allow 172.26.0.0/24;
    allow 172.25.0.0/24;
    allow 192.168.12.0/22;
    deny all;
}
```

## Riesgos específicos

- 🔴 **Versión del plugin no verificada en producción** — el archivo `all-in-one-wp-migration.6.77.zip` está versionado en el repo pero no hay certeza de que sea la versión instalada en el sitio activo.
- ⚠️ **Backups contienen credenciales** — los archivos `.wpress` incluyen la base de datos completa (con hashes de contraseñas) y el `wp-config.php` con credenciales de DB. Deben almacenarse de forma segura.
- 🟡 **Sin programación automática de backups** — no hay cronjob ni configuración automática visible en el repositorio. Los backups son manuales desde `/wp-admin`.
- ⚠️ **Archivo `.zip` del plugin versionado en Git** — tener binarios de plugins en el repositorio no es una práctica recomendada.

## Archivos fuente relevantes

- `all-in-one-wp-migration.6.77.zip` — archivo del plugin (versionado en repo)
- `nginx-conf/nginx.conf` — regla de bloqueo de acceso a backups (línea ~73)

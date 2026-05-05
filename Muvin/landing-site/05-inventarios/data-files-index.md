# Índice de Archivos de Datos — Landing Site Muvin

| Archivo | Ruta | Propósito | Formato | ¿Versionado? | ¿Se regenera? | Notas |
|---------|------|-----------|---------|-------------|---------------|-------|
| `docker-compose.yml` | `/` | Definición de servicios Docker | YAML | ✅ Sí | Manual | Archivo principal de orquestación |
| `.env` | `/` | Credenciales MySQL (ROOT, USER, PASSWORD) | Texto plano (KEY=VALUE) | ❌ No | Manual por operador | 🔴 Contiene secretos — no versionar |
| `nginx.conf` | `nginx-conf/` | Configuración activa de Nginx | Nginx config | ✅ Sí | Manual | Configuración de producción activa |
| `nginx.conf.new` | `nginx-conf/` | Configuración candidata de Nginx | Nginx config | ✅ Sí | Manual | ⚠️ No activa — estado incierto |
| `allowip.ip` | `nginx-conf/` | Whitelist de IPs para wp-login | Nginx include (IPs/rangos) | ✅ Sí | Manual por operador | 🔒 Impacto en seguridad si cambia |
| `all-in-one-wp-migration.6.77.zip` | `/` | Plugin de backup/migración WordPress | ZIP (binario) | ✅ Sí | No (binario estático) | ⚠️ Binario en Git — no recomendado |
| `wp-config.php` | `/opt/landingpage/wordpress/` | Configuración WordPress con credenciales DB | PHP | ❌ No (generado por imagen) | Automático en primer arranque | 🔴 Contiene credenciales DB |
| Base de datos MySQL | `/opt/landingpage/dbdata/` | Datos del CMS (contenido, usuarios, config) | MySQL binario | ❌ No (volumen host) | Automático | 🔴 No está respaldada en repo |
| Archivos WordPress | `/opt/landingpage/wordpress/` | Código WP, temas, plugins, uploads | PHP, HTML, medios | ❌ No (volumen host) | Imagen Docker base | ⚠️ Plugins y temas adicionales no visibles |

## Archivos de backup

| Archivo | Formato | Destino | Notas |
|---------|---------|---------|-------|
| `*.wpress` | Propietario all-in-one-wp-migration | `/wp-content/ai1wm-backups/` | 🔒 Acceso restringido por IP en Nginx |
| `*.zip` / `*.gz` (updraft) | ZIP/GZ | `/wp-content/updraft/` | 🔒 Acceso restringido por IP en nginx.conf (removido en nginx.conf.new) |

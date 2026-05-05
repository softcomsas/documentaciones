# Stack Tecnológico — Landing Site Muvin

| Tecnología | Versión | Propósito | Soporte vendor | Riesgo |
|------------|---------|-----------|----------------|--------|
| WordPress | 5.3.2 | CMS / motor del sitio | 🔴 EOL (última WP 5.x es 5.9.x; actual es 6.x) | 🔴 Alto — sin parches de seguridad |
| MySQL | 5.7 | Base de datos relacional | 🔴 EOL (Oct 2023) | 🔴 Alto — sin actualizaciones de seguridad |
| Nginx | 1.15.12 | Proxy inverso / webserver | 🔴 EOL (rama 1.15 mainline, año 2019) | 🔴 Alto — versión muy desactualizada |
| PHP (via WP FPM) | ⚠️ Pendiente de verificar | Runtime de WordPress | ⚠️ Depende de imagen base | 🟡 Medio |
| Docker | ⚠️ Pendiente de verificar | Containerización | 🟢 Vigente | 🟢 Bajo |
| Docker Compose | v3 (sintaxis) | Orquestación local | 🟢 Vigente | 🟢 Bajo |
| Alpine Linux | base de imágenes | SO base de contenedores | 🟢 Vigente | 🟢 Bajo |
| all-in-one-wp-migration | 6.77 | Backups y migración WP | ⚠️ Pendiente de verificar | 🟡 Medio |

## Notas de riesgo

> [!danger] Versiones EOL
> WordPress 5.3.2, MySQL 5.7 y Nginx 1.15.12 están todos en **End of Life**. No reciben parches de seguridad. Cualquier vulnerabilidad descubierta en estas versiones queda sin remediar. Se recomienda actualización urgente.

> [!warning] PHP no documentado
> La versión exacta de PHP no es determinable desde el código versionado. La imagen `wordpress:5.3.2-fpm-alpine` usa PHP 7.3 aproximadamente, también EOL.

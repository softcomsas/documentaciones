# Inventario de Seguridad — Landing Site Muvin

> **Fecha de auditoría:** 2026-05-05
> **Fuentes analizadas:** `docker-compose.yml`, `nginx-conf/nginx.conf`, `nginx-conf/nginx.conf.new`, `nginx-conf/allowip.ip`, `README.md`

---

## 🔒 Usuarios y roles

| Rol | Definido en | Notas |
|-----|-------------|-------|
| Administrador WordPress | WordPress DB (`wp_users`) | ⚠️ No visible en el código versionado. Credenciales en producción. |
| Usuario MySQL de aplicación | `.env` (`MYSQL_USER`) | ⚠️ Credenciales no versionadas (correcto). Verificar permisos mínimos. |
| Root MySQL | `.env` (`MYSQL_ROOT_PASSWORD`) | 🔴 Contraseña root requerida — verificar si es estrictamente necesaria. |

---

## 🔴 Hallazgos críticos

### SEC-001 — Versiones EOL en toda la pila
| Severidad | 🔴 Alta |
|-----------|---------|
| Componentes | WordPress 5.3.2, MySQL 5.7, Nginx 1.15.12, PHP ~7.3 |
| Descripción | Todos los componentes principales están en End of Life. No reciben parches de seguridad. Cualquier CVE publicado queda sin remediar. |
| Ubicación | `docker-compose.yml` — líneas de imagen |
| Recomendación | Actualizar WordPress a 6.x, MySQL a 8.x, Nginx a 1.24+ o 1.25+. |

### SEC-002 — `/wp-admin/` sin restricción de IP en Nginx
| Severidad | 🔴 Alta |
|-----------|---------|
| Descripción | `wp-login.php` tiene whitelist IP, pero el directorio `/wp-admin/` en su totalidad no. Un atacante puede acceder directamente a rutas de `/wp-admin/` sin restricción IP, dependiendo solo de la autenticación de WordPress. |
| Ubicación | `nginx-conf/nginx.conf` — ausencia de bloque `location ~* /wp-admin/` |
| Recomendación | Agregar bloque `location ~* /wp-admin/` con restricción IP equivalente a la de `wp-login.php`. |

### SEC-003 — Credenciales en archivo `.env` sin cifrar
| Severidad | 🔴 Alta |
|-----------|---------|
| Descripción | Las credenciales de MySQL (root, usuario, contraseña) se almacenan en un archivo `.env` en texto plano en el servidor. Si el servidor es comprometido, las credenciales están expuestas. |
| Ubicación | `.env` (no versionado), `docker-compose.yml` referencia `env_file: .env` |
| Recomendación | Usar Docker Secrets, HashiCorp Vault, o AWS SSM Parameter Store. |

---

## 🟡 Hallazgos de severidad media

### SEC-004 — Rate limiting de wp-login comentado
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | El bloque `limit_req_zone` para `wp-login.php` está comentado en `nginx.conf`. Sin rate limiting activo, ataques de fuerza bruta son posibles desde IPs no whitelisteadas (mitigado parcialmente por restricción IP). |
| Ubicación | `nginx-conf/nginx.conf` líneas ~109-112 (comentadas) |
| Recomendación | Descomentar y activar el rate limiting. |

### SEC-005 — XML-RPC habilitado
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | `/xmlrpc.php` está restringido por IP pero no deshabilitado. Si el acceso no es necesario, la superficie de ataque puede reducirse eliminándolo. |
| Ubicación | `nginx-conf/nginx.conf` líneas ~31-36 |
| Recomendación | Si no se usa la API XML-RPC, deshabilitar completamente en WordPress o bloquear a nivel Nginx para todas las IPs. |

### SEC-006 — `nginx.conf.new` sin `autoindex off`
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | La configuración candidata `nginx.conf.new` no incluye la directiva `autoindex off`. Si se despliega, habilitaría el listado de directorios web. |
| Ubicación | `nginx-conf/nginx.conf.new` |
| Recomendación | Agregar `autoindex off` a `nginx.conf.new` antes de cualquier despliegue. |

### SEC-007 — HSTS básico (sin `includeSubDomains` ni `preload`)
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | El header `Strict-Transport-Security` está configurado con `max-age=31536000` pero sin `includeSubDomains` ni `preload`. Subdominios no están cubiertos. |
| Ubicación | `nginx-conf/nginx.conf` línea ~11 |
| Recomendación | Agregar `; includeSubDomains; preload` si aplica. |

### SEC-008 — Binario de plugin versionado en Git
| Severidad | 🟡 Media |
|-----------|---------|
| Descripción | `all-in-one-wp-migration.6.77.zip` está versionado en el repositorio. Los archivos binarios en Git aumentan el tamaño del repo y pueden contener vulnerabilidades sin actualizarse. |
| Ubicación | `all-in-one-wp-migration.6.77.zip` |
| Recomendación | Eliminar del repo, descargar en CI/CD o gestionar con WP CLI. |

---

## 🟢 Controles de seguridad positivos identificados

| Control | Descripción | Ubicación |
|---------|-------------|-----------|
| ✅ Headers HTTP de seguridad | X-Frame-Options, HSTS, X-Content-Type-Options, X-XSS-Protection | `nginx.conf` líneas 8-12 |
| ✅ `server_tokens off` | Oculta versión de Nginx en responses | `nginx.conf` línea 9 |
| ✅ PHP version oculta | `fastcgi_hide_header X-Powered-By` | `nginx.conf` líneas ~151-152 |
| ✅ Bloqueo de PHP en uploads | Evita ejecución de webshells subidos | `nginx.conf` líneas ~43-51 |
| ✅ Bloqueo de dotfiles | `.git`, `.ht`, `.user.ini` inaccesibles | `nginx.conf` líneas ~53-65 |
| ✅ Restricción de métodos HTTP | Solo GET y POST; 444 para el resto | `nginx.conf` líneas ~38-40 |
| ✅ Whitelist IP en wp-login | Acceso restringido a redes internas | `nginx.conf` líneas ~136-148 |
| ✅ Whitelist IP en xmlrpc | Acceso restringido a redes internas | `nginx.conf` líneas ~31-36 |
| ✅ Bloqueo de backup `.wpress` | Archivos de backup no accesibles externamente | `nginx.conf` líneas ~72-78 |
| ✅ MySQL no expuesto al host | Puerto 3306 solo en red Docker interna | `docker-compose.yml` |

---

## Endpoints sin autenticación

| Endpoint | Autenticación | Riesgo |
|----------|--------------|--------|
| `GET /*` (páginas públicas) | Ninguna | 🟢 Esperado — es un sitio público |
| `GET /wp-cron.php` | Ninguna (restringido IP) | 🟡 Sin autenticación real |
| `GET /wp-json/*` (REST API WP) | Ninguna para endpoints públicos | ⚠️ Pendiente de verificar alcance |

---

## Logs con datos sensibles

| Log | Riesgo | Ubicación |
|-----|--------|-----------|
| Logs de acceso Nginx | Pueden registrar URLs con tokens/params en query string | Nginx stdout/stderr en Docker |
| Logs de WordPress | Pueden incluir stack traces con paths del servidor | `/var/www/html/wp-content/debug.log` si `WP_DEBUG_LOG` activo |

> ⚠️ No es posible verificar la configuración de `WP_DEBUG_LOG` desde el código versionado.

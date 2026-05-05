# Glosario — Landing Site Muvin

| Término | Definición |
|---------|-----------|
| **WP / WordPress** | Sistema de gestión de contenidos (CMS) open source sobre PHP+MySQL. |
| **FPM** | FastCGI Process Manager. Variante de PHP que se comunica vía protocolo FastCGI, usado aquí por Nginx para delegar la ejecución de PHP. |
| **FastCGI** | Protocolo de comunicación entre el servidor web (Nginx) y el intérprete PHP (WordPress FPM). Puerto `9000`. |
| **wp-admin** | Panel de administración de WordPress. Ruta: `/wp-admin`. Acceso restringido por IP en este proyecto. |
| **wp-login.php** | Página de inicio de sesión de WordPress. Restringida por IP en `nginx.conf`. |
| **xmlrpc.php** | API XML-RPC de WordPress. Vector de ataques histórico. Restringida por IP en este proyecto. |
| **wp-cron.php** | Scheduler interno de WordPress para tareas programadas. Restringido por IP. |
| **app-network** | Red Docker interna de tipo `bridge` que conecta los tres contenedores. |
| **all-in-one-wp-migration** | Plugin de WordPress para exportar/importar el sitio completo (archivos + DB) en un único archivo `.wpress`. |
| **.env** | Archivo de variables de entorno con credenciales de base de datos. No debe versionarse. |
| **allowip.ip** | Archivo incluido en `nginx.conf` con IPs adicionales permitidas para acceder a `wp-login.php`. |
| **nginx.conf.new** | Versión candidata de la configuración Nginx. Tiene menos reglas que `nginx.conf` (sin `autoindex off`, sin bloqueo de updraft). ⚠️ No está activa. |
| **muvinapp.com** | Dominio público del sitio. |
| **WPRATELIMIT** | Zona de rate-limiting para wp-login definida en nginx.conf (actualmente comentada). |
| **Volumen persistente** | Directorio del host montado en el contenedor. Persiste datos al reiniciar contenedores. |
| **BCR** | Sigla usada en comentarios de nginx.conf para identificar rangos de red internos (172.17.x, 172.16.x, etc.). Probablemente "Back-end Corporate Ranges". ⚠️ Pendiente de verificar. |

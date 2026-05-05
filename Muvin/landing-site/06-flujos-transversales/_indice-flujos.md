# Índice de Flujos Transversales — Landing Site Muvin

| # | Flujo | Descripción | Módulos involucrados | Archivo |
|---|-------|-------------|---------------------|---------|
| 1 | Solicitud de página pública | Request de usuario → Nginx → WordPress → MySQL → Response HTML | Nginx, WordPress, MySQL | [[flujo-request-pagina-publica]] |
| 2 | Login de administrador | Admin accede a wp-login.php con restricción IP | Nginx, WordPress, MySQL | [[flujo-login-admin]] |
| 3 | Backup del sitio | Generación y descarga de backup con all-in-one-wp-migration | WordPress, Nginx | [[flujo-backup]] |
| 4 | Arranque del stack | Secuencia de inicio de servicios con Docker Compose | Docker Compose, MySQL, WordPress, Nginx | [[flujo-arranque-stack]] |

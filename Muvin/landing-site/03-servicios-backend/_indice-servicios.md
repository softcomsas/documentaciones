# Índice de Servicios Backend — Landing Site Muvin

> [!info]
> El landing site no expone una API REST propia. Los únicos "servicios" son los endpoints nativos de WordPress, todos gestionados internamente. No hay microservicios propios ni integraciones REST documentadas en el código versionado.

| Endpoint | Descripción | Estado | Detalle |
|----------|-------------|--------|---------|
| `POST /wp-login.php` | Login de administrador WordPress | 🔒 Restringido por IP | [[wp-endpoints#wp-login]] |
| `POST /xmlrpc.php` | API XML-RPC de WordPress | 🔒 Restringido por IP | [[wp-endpoints#xmlrpc]] |
| `GET/POST /wp-cron.php` | Scheduler interno de WordPress | 🔒 Restringido por IP | [[wp-endpoints#wp-cron]] |
| `GET /wp-admin/*` | Panel de administración | 🔐 Solo autenticados (sin restricción IP en Nginx) | [[wp-endpoints#wp-admin]] |
| `GET|POST /*` | Páginas públicas WordPress | 🟢 Público | [[wp-endpoints#publico]] |

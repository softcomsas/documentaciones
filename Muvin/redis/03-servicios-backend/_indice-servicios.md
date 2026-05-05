# Índice de Servicios — Redis Muvin

> Este repositorio **es** el servicio. No consume servicios backend propios. Documenta las integraciones externas de las que depende.

## Integraciones externas

| Servicio | Tipo | URL / Endpoint | Rol |
|----------|------|---------------|-----|
| [[servicio-vault]] | Gestión de secretos | `https://vault.alternativasinteligentes.com` | Provee credenciales SSH |
| [[servicio-docker-hub]] | Registry de imágenes | `hub.docker.com` | Fuente de `redis:7` |
| Servidor DEV | Linux + Docker | `$SSH_HOST` (desde Vault) | Destino del deploy |
| Repo externo cap | Git (GitLab?) | `$TARGET_URL` | Espejo del repo |

## Consumidores de Redis en el ecosistema

| Servicio | Cómo usa Redis |
|----------|---------------|
| `sockets` (Node.js) | Pub/Sub en tiempo real |
| `api` (Yii2/PHP) | Caché de datos / colas |

# Glosario — Redis Muvin

| Término | Definición |
|---------|-----------|
| **Redis** | Base de datos en memoria de código abierto. Soporta estructuras de datos como strings, hashes, listas, sets y pub/sub. |
| **muvin-net** | Red Docker de tipo bridge compartida entre los servicios del stack Muvin en cada servidor. |
| **muvin-redis** | Nombre del contenedor Docker del servicio Redis en Muvin. |
| **Vault** | HashiCorp Vault — gestor centralizado de secretos. En Muvin almacena las credenciales SSH en la ruta `muvin/data/dev_ssh`. |
| **VAULT_TOKEN** | Secret de GitHub que autentica al runner de GitHub Actions contra Vault. |
| **SSH_HOST** | IP o hostname del servidor destino. Recuperado desde Vault. |
| **SSH_USER** | Usuario SSH del servidor destino. Recuperado desde Vault. |
| **SSH_PRIVATE_KEY** | Clave privada SSH para autenticación sin contraseña. Recuperado desde Vault. |
| **deploy-dev.yml** | Workflow de GitHub Actions que despliega Redis en el ambiente dev. |
| **sync-cap.yml** | Workflow que sincroniza el repositorio GitHub hacia el repositorio espejo de cap. |
| **Pub/Sub** | Patrón publicador/suscriptor — Redis permite que un servicio publique mensajes en un canal y otros servicios los reciban en tiempo real. |
| **Volatile cache** | Caché sin persistencia. Los datos se pierden al reiniciar el contenedor. |

## Referencias

- [[vision-general]]

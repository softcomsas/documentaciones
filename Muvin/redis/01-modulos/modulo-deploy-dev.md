# Módulo: Deploy Dev (GitHub Actions)

## Descripción

Workflow de GitHub Actions que despliega automáticamente el servicio Redis en el ambiente de desarrollo al hacer push sobre la rama `dev`.

## Archivo: `.github/workflows/deploy-dev.yml`

### Trigger

```yaml
on:
  push:
    branches:
      - dev
```

### Jobs y steps

| Step | Acción | Detalle |
|------|--------|---------|
| 1 | `actions/checkout@v4` | Clona el repositorio |
| 2 | `hashicorp/vault-action@v2.4.0` | Importa secretos SSH desde Vault |
| 3 | Setup SSH Key | Escribe la clave privada en `~/.ssh/id_rsa` con permisos `600` |
| 4 | Deploy Redis | Ejecuta script bash remoto vía SSH |

### Script de deploy remoto

```bash
#!/bin/bash
set -e

NETWORK_NAME="muvin-net"
CONTAINER_NAME="muvin-redis"
IMAGE="redis:7"

# Verificar y crear red si no existe
if ! docker network inspect $NETWORK_NAME > /dev/null 2>&1; then
  docker network create $NETWORK_NAME
fi

# Eliminar contenedor previo (idempotente)
docker rm -f $CONTAINER_NAME || true

# Crear contenedor nuevo
docker run -d \
  --restart always \
  --name $CONTAINER_NAME \
  --network $NETWORK_NAME \
  -p 6379:6379 \
  $IMAGE
```

### Secretos requeridos en GitHub

| Secret en GitHub | Origen | Descripción |
|-----------------|--------|-------------|
| `VAULT_TOKEN` | GitHub Secrets | Token de autenticación en Vault |

### Secretos importados desde Vault

| Ruta en Vault | Variable de entorno | Descripción |
|---------------|---------------------|-------------|
| `muvin/data/dev_ssh → SSH_HOST` | `SSH_HOST` | IP/hostname del servidor dev |
| `muvin/data/dev_ssh → SSH_PRIVATE_KEY` | `SSH_PRIVATE_KEY` | Clave privada SSH |
| `muvin/data/dev_ssh → SSH_USER` | `SSH_USER` | Usuario SSH |

## Ventajas sobre el enfoque de config-deploys

| Aspecto | config-deploys | redis (este repo) |
|---------|---------------|------------------|
| Secretos SSH | Variables GitLab en texto plano | HashiCorp Vault |
| Autenticación | `sshpass` + contraseña | Clave SSH (✅ seguro) |
| Visibilidad en `ps aux` | Contraseña expuesta | No expuesta |

## Referencias

- [[modulo-redis-container]]
- [[modulo-sync-cap]]
- [[flujo-deploy-dev]]

# Flujo: Deploy Dev

## Diagrama completo

```mermaid
sequenceDiagram
    participant Dev as Desarrollador
    participant GH as GitHub
    participant GHA as Runner ubuntu-latest
    participant Vault as HashiCorp Vault
    participant Srv as Servidor DEV

    Dev->>GH: git push origin dev

    GH->>GHA: trigger deploy-dev.yml

    GHA->>GHA: actions/checkout@v4
    Note over GHA: Código descargado

    GHA->>Vault: GET https://vault.../muvin/data/dev_ssh\n(token: VAULT_TOKEN)
    Vault-->>GHA: SSH_HOST, SSH_USER, SSH_PRIVATE_KEY

    GHA->>GHA: echo "$SSH_PRIVATE_KEY" > ~/.ssh/id_rsa\nchmod 600 ~/.ssh/id_rsa

    GHA->>GHA: escribir script en /tmp/muvin_redis_deploy.sh

    GHA->>Srv: ssh -o StrictHostKeyChecking=no\n$SSH_USER@$SSH_HOST bash -s < script

    Srv->>Srv: docker network inspect muvin-net
    alt Red no existe
        Srv->>Srv: docker network create muvin-net
    end

    Srv->>Srv: docker rm -f muvin-redis || true

    Srv->>Srv: docker run -d --restart always\n--name muvin-redis\n--network muvin-net\n-p 6379:6379 redis:7

    Srv-->>GHA: exit 0
    GHA-->>GH: ✅ workflow completed
```

## Tiempo estimado de ejecución

| Step | Tiempo aprox. |
|------|--------------|
| Checkout | ~5s |
| Vault action | ~5s |
| Setup SSH | ~1s |
| Deploy (SSH + Docker) | ~15-30s (pull de imagen si no está en caché) |
| **Total** | **~30-45s** |

## Ventana de downtime

Entre `docker rm -f` y `docker run` hay ~2-5 segundos donde Redis no está disponible. Los clientes conectados perderán conexión momentáneamente.

## Referencias

- [[modulo-deploy-dev]]
- [[funcionalidad-deploy-automatico]]
- [[funcionalidad-idempotencia-deploy]]

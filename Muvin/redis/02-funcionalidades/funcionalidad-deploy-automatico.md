# Funcionalidad: Deploy Automático

## Descripción

Al hacer `git push` sobre la rama `dev`, GitHub Actions despliega Redis en el servidor de desarrollo sin intervención manual.

## Flujo

```mermaid
sequenceDiagram
    participant Dev as Desarrollador
    participant GH as GitHub
    participant GHA as GitHub Actions Runner
    participant Vault as HashiCorp Vault
    participant Srv as Servidor DEV

    Dev->>GH: git push origin dev
    GH->>GHA: trigger deploy-dev.yml
    GHA->>Vault: GET muvin/data/dev_ssh
    Vault-->>GHA: SSH_HOST, SSH_USER, SSH_PRIVATE_KEY
    GHA->>GHA: escribir ~/.ssh/id_rsa (chmod 600)
    GHA->>Srv: SSH → script bash remoto
    Srv->>Srv: verificar/crear red muvin-net
    Srv->>Srv: docker rm -f muvin-redis
    Srv->>Srv: docker run -d redis:7 --network muvin-net -p 6379:6379
    Srv-->>GHA: exit 0
    GHA-->>GH: workflow ✅
```

## Precondiciones

- La rama `dev` debe existir y recibir el push.
- El secret `VAULT_TOKEN` debe estar configurado en GitHub Secrets.
- Vault debe estar accesible en `https://vault.alternativasinteligentes.com`.
- El servidor destino debe tener Docker instalado.
- El usuario SSH debe tener permisos para ejecutar `docker` (grupo `docker` o `sudo`).

## Postcondiciones

- Contenedor `muvin-redis` corriendo con `redis:7`.
- Puerto `6379` expuesto en el host.
- Red `muvin-net` existente y el contenedor conectado a ella.
- `--restart always` garantiza que Redis se levanta tras reinicios del servidor.

## Referencias

- [[modulo-deploy-dev]]
- [[funcionalidad-idempotencia-deploy]]

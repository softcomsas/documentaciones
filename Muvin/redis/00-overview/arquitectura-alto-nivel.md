# Arquitectura de Alto Nivel — Redis Muvin

## Diagrama de despliegue

```mermaid
graph TB
    subgraph GitHub
        REPO[Repositorio\nredis]
        WF1[deploy-dev.yml]
        WF2[sync-cap.yml]
    end

    subgraph Vault
        V[HashiCorp Vault\nvault.alternativasinteligentes.com\nmuvin/data/dev_ssh]
    end

    subgraph Servidor DEV
        NET[Red: muvin-net\nbridge]
        REDIS[Contenedor: muvin-redis\nredis:7\n:6379]
        NET --- REDIS
    end

    subgraph Repo Externo Cap
        CAP[Repo cap\n mirror]
    end

    REPO --> WF1
    REPO --> WF2
    WF1 -->|leer secretos SSH| V
    WF1 -->|SSH + bash script| Servidor DEV
    WF2 -->|git push mirror| CAP
```

## Flujo de deploy simplificado

```
git push → dev branch
    → GitHub Actions trigger
    → importar SSH_HOST, SSH_USER, SSH_PRIVATE_KEY desde Vault
    → conectar por SSH al servidor
    → ejecutar script bash remoto:
        - verificar/crear red muvin-net
        - docker rm -f muvin-redis
        - docker run -d redis:7 --network muvin-net -p 6379:6379
```

## Decisiones de diseño

| Decisión | Razón |
|----------|-------|
| Script bash inline en el workflow | Simplicidad — no requiere archivos adicionales en el repo |
| Red `muvin-net` creada si no existe | Idempotencia del deploy |
| `docker rm -f` antes de recrear | Evita conflictos de nombre de contenedor |
| Vault para secretos SSH | Centralización segura de credenciales (vs variables GitLab) |
| Sin `docker-compose` en el deploy real | El `docker/docker-compose.yml` es solo para uso local/referencia |

## Referencias

- [[vision-general]]
- [[stack-tecnologico]]

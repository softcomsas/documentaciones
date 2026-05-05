# Índice de Módulos — Redis Muvin

| Módulo | Archivo | Descripción |
|--------|---------|-------------|
| [[modulo-redis-container]] | `docker/docker-compose.yml` | Definición del contenedor Redis |
| [[modulo-deploy-dev]] | `.github/workflows/deploy-dev.yml` | Pipeline de deploy automático en dev |
| [[modulo-sync-cap]] | `.github/workflows/sync-cap.yml` | Sincronización del repo hacia cap |

## Mapa de dependencias

```mermaid
graph LR
    DC[modulo-redis-container] -->|referencia| DD[modulo-deploy-dev]
    DD -->|usa secretos de| VAULT[HashiCorp Vault]
    DD -->|deploy SSH a| SRV[Servidor DEV]
    SC[modulo-sync-cap] -->|mirror push| REPO_CAP[Repo externo cap]
```

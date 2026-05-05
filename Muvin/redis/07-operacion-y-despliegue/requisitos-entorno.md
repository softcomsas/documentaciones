# Requisitos de Entorno — Redis Muvin

## Servidor de deploy

| Requisito | Versión mínima | Notas |
|-----------|---------------|-------|
| Docker Engine | ≥ 20.x | Debe estar instalado y corriendo |
| Usuario SSH | — | Con permiso de ejecutar `docker` (grupo `docker`) |
| Conectividad a Docker Hub | — | Para pull de `redis:7` |
| Puerto 6379 disponible | — | No debe estar ocupado por otro proceso |

## GitHub Actions

| Requisito | Descripción |
|-----------|-------------|
| `VAULT_TOKEN` | Secret en el repositorio GitHub con token válido de Vault |
| Runner | `ubuntu-latest` (hosted runner de GitHub) |

## HashiCorp Vault

| Requisito | Descripción |
|-----------|-------------|
| Accesible | `https://vault.alternativasinteligentes.com` debe responder |
| Ruta configurada | `muvin/data/dev_ssh` con claves `SSH_HOST`, `SSH_USER`, `SSH_PRIVATE_KEY` |
| Token vigente | El `VAULT_TOKEN` almacenado en GitHub no debe haber expirado |

## Ambientes no configurados

> [!warning]
> Los ambientes **uat** y **prd** no tienen workflows de deploy en este repositorio. Si se requiere Redis en esos ambientes, deben crearse los workflows correspondientes (`deploy-uat.yml`, `deploy-prd.yml`) y configurar los secretos en Vault.

## Referencias

- [[build-y-despliegue]]
- [[arquitectura-alto-nivel]]

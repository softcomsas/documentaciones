# Stack Tecnológico — Redis Muvin

## Componentes

| Capa | Tecnología | Versión | Rol |
|------|-----------|---------|-----|
| Base de datos en memoria | Redis | `7` | Servicio principal |
| Contenedores | Docker Engine | ≥ 20.x | Runtime |
| Orquestación local | Docker Compose | v3.9 | Referencia local |
| CI/CD | GitHub Actions | — | Deploy y sync |
| Gestión de secretos | HashiCorp Vault | — | SSH credentials |
| Red de contenedores | Docker Bridge | — | `muvin-net` |

## Imagen Docker

```
redis:7
```
- Fuente: Docker Hub oficial.
- Sin imagen customizada — se usa la oficial sin modificaciones.
- Sin `Dockerfile` en el repositorio.

## Comparación con uso en config-deploys

> [!info]
> En el repo `config-deploys`, los sockets usan `redis:7.0.4-alpine` (imagen más liviana). Este repo despliega `redis:7` (imagen completa Debian-based) como servicio independiente para otros consumidores del stack.

| Atributo | redis (este repo) | redis en sockets |
|----------|------------------|-----------------|
| Imagen | `redis:7` | `redis:7.0.4-alpine` |
| Puerto host | `6379` | no expuesto externamente |
| Red | `muvin-net` | red interna del compose |
| Deploy | GitHub Actions + Vault | GitLab CI + sshpass |
| Persistencia | No | No |

## Referencias

- [[vision-general]]
- [[arquitectura-alto-nivel]]

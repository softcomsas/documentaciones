# Módulo: Contenedor Redis

## Descripción

Define el servicio Redis como contenedor Docker. El archivo `docker/docker-compose.yml` sirve como **referencia y para uso local**. En producción/dev el deploy se realiza mediante el script bash del workflow `deploy-dev.yml` (no con `docker-compose`).

## Archivo: `docker/docker-compose.yml`

```yaml
version: "3.9"

services:
  redis:
    container_name: muvin-redis
    image: redis:7
    ports:
      - "6379:6379"
    networks:
      - muvin-net-dev
    restart: unless-stopped

networks:
  muvin-net-dev:
    driver: bridge
```

> [!warning] Diferencia con el deploy real
> En el deploy via GitHub Actions, la red se llama `muvin-net` (sin sufijo `-dev`). El docker-compose usa `muvin-net-dev`. Esta inconsistencia puede causar confusión.

## Parámetros del contenedor

| Parámetro | Valor | Descripción |
|-----------|-------|-------------|
| `container_name` | `muvin-redis` | Nombre fijo del contenedor |
| `image` | `redis:7` | Imagen oficial Docker Hub |
| `ports` | `6379:6379` | Puerto estándar Redis expuesto al host |
| `restart` | `unless-stopped` (compose) / `always` (deploy real) | Política de reinicio |
| `network` | `muvin-net-dev` (compose) / `muvin-net` (deploy) | Red compartida con otros servicios |

## Sin volúmenes de persistencia

> [!caution]
> No hay volumen configurado (`volumes:`). Los datos de Redis **se pierden** cuando el contenedor se detiene o reinicia. Si Redis se usa para almacenar sesiones o datos con durabilidad requerida, esto es un riesgo. Ver [[deuda-tecnica]].

## Referencias

- [[modulo-deploy-dev]]
- [[vision-general]]

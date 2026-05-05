# Build y Despliegue — Redis Muvin

## Deploy automático (recomendado)

```bash
# Simplemente hacer push a la rama dev:
git push origin dev
# → GitHub Actions ejecuta deploy-dev.yml automáticamente
```

## Deploy manual (emergencias)

Si el pipeline falla, se puede desplegar manualmente conectándose al servidor:

```bash
# 1. Conectarse al servidor
ssh $SSH_USER@$SSH_HOST

# 2. Asegurar que la red existe
docker network inspect muvin-net > /dev/null 2>&1 || docker network create muvin-net

# 3. Eliminar contenedor previo
docker rm -f muvin-redis || true

# 4. Levantar Redis
docker run -d \
  --restart always \
  --name muvin-redis \
  --network muvin-net \
  -p 6379:6379 \
  redis:7

# 5. Verificar que está corriendo
docker ps | grep muvin-redis
docker exec muvin-redis redis-cli ping
# Respuesta esperada: PONG
```

## Verificar estado del servicio

```bash
# Estado del contenedor
docker ps -f name=muvin-redis

# Logs en tiempo real
docker logs -f muvin-redis

# Test de conectividad Redis
docker exec muvin-redis redis-cli ping

# Info del servidor Redis
docker exec muvin-redis redis-cli info server | grep redis_version
```

## Detener el servicio

```bash
docker stop muvin-redis
# o eliminar completamente:
docker rm -f muvin-redis
```

## Uso local con docker-compose

```bash
cd docker/
docker-compose up -d

# Verificar
docker-compose ps
docker-compose logs redis
```

> [!note]
> El docker-compose usa la red `muvin-net-dev`. Asegurarse de que los otros servicios locales también usen esa red.

## Referencias

- [[requisitos-entorno]]
- [[flujo-deploy-dev]]

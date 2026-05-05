# Deuda Técnica — Redis Muvin

| # | Deuda | Impacto | Esfuerzo | Prioridad |
|---|-------|---------|----------|-----------|
| DT-01 | Agregar `requirepass` a Redis | 🔴 Alto | 🟢 Bajo | **P1** |
| DT-02 | Cambiar `-p 6379:6379` a `-p 127.0.0.1:6379:6379` | 🔴 Alto | 🟢 Bajo | **P1** |
| DT-03 | Corregir `StrictHostKeyChecking=no` → agregar `known_hosts` | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-04 | Anclar acciones GitHub a SHA (`checkout@v2` → `checkout@<sha>`) | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-05 | Agregar volumen de persistencia si los datos son críticos | 🟡 Medio | 🟡 Medio | **P2** |
| DT-06 | Crear workflows para UAT y PRD | 🟡 Medio | 🟡 Medio | **P2** |
| DT-07 | Unificar nombre de red (`muvin-net-dev` vs `muvin-net`) | 🟢 Bajo | 🟢 Bajo | **P3** |
| DT-08 | Agregar `maxmemory` y `maxmemory-policy` a la configuración | 🟡 Medio | 🟢 Bajo | **P2** |
| DT-09 | Agregar README.md al repositorio | 🟢 Bajo | 🟢 Bajo | **P3** |

## Detalle de deudas P1

### DT-01 — Agregar contraseña a Redis

```bash
# En deploy-dev.yml, cambiar el docker run a:
docker run -d \
  --restart always \
  --name $CONTAINER_NAME \
  --network $NETWORK_NAME \
  -p 127.0.0.1:6379:6379 \
  $IMAGE \
  redis-server --requirepass "$REDIS_PASSWORD"
```

Agregar en Vault la clave `REDIS_PASSWORD` en `muvin/data/dev_redis` y recuperarla en el workflow:
```yaml
- uses: hashicorp/vault-action@v2.4.0
  with:
    secrets: |
      muvin/data/dev_redis REDIS_PASSWORD;
```

### DT-02 — Restringir bind del puerto

Cambiar `-p 6379:6379` por `-p 127.0.0.1:6379:6379` para que Redis solo sea accesible desde localhost del servidor host (no desde la red del servidor).

### DT-08 — Configurar maxmemory

Sin límite de memoria, Redis puede consumir toda la RAM disponible y causar OOM en el servidor. Configuración recomendada:

```bash
redis-server \
  --requirepass "$REDIS_PASSWORD" \
  --maxmemory 256mb \
  --maxmemory-policy allkeys-lru
```

## Referencias

- [[hotspots]]
- [[security-inventory]]
- [[recomendaciones-modernizacion]]

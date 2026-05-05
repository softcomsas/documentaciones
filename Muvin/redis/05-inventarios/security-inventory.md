# Inventario de Seguridad — Redis Muvin

| ID | Hallazgo | Severidad | Archivo | Estado |
|----|---------|-----------|---------|--------|
| SEC-001 | Redis sin contraseña (`requirepass` no configurado) | 🔴 Alto | `docker-compose.yml` / script deploy | Abierto |
| SEC-002 | Redis bind en `0.0.0.0` — accesible desde cualquier host en la red | 🔴 Alto | script deploy | Abierto |
| SEC-003 | Puerto `6379` expuesto al host (`-p 6379:6379`) sin restricción de IP | 🟡 Medio | `docker-compose.yml` / script deploy | Abierto |
| SEC-004 | `actions/checkout@v2` sin anclar a SHA (supply chain risk) | 🟡 Medio | `sync-cap.yml` | Abierto |
| SEC-005 | `wangchucheng/git-repo-sync@v0.1.0` acción de tercero sin SHA | 🟡 Medio | `sync-cap.yml` | Abierto |
| SEC-006 | Sin persistencia — datos volátiles pueden perderse sin aviso | 🟡 Medio | Configuración Redis | Abierto |
| SEC-007 | `StrictHostKeyChecking=no` en SSH — vulnerable a MITM | 🟡 Medio | `deploy-dev.yml` | Abierto |

## Detalle de hallazgos críticos

### SEC-001 — Redis sin contraseña

Redis acepta cualquier conexión sin autenticación. Cualquier proceso en la red `muvin-net` puede leer y escribir datos.

**Corrección:**
```bash
docker run -d \
  --name muvin-redis \
  --network muvin-net \
  -p 127.0.0.1:6379:6379 \
  redis:7 \
  redis-server --requirepass "$REDIS_PASSWORD"
```

### SEC-003 — Puerto expuesto al host

Con `-p 6379:6379`, Redis es accesible desde el host y potencialmente desde la red del servidor (dependiendo del firewall). Cambiar a `-p 127.0.0.1:6379:6379` limita el acceso a localhost únicamente.

### SEC-007 — StrictHostKeyChecking=no

```bash
# Actual (vulnerable a MITM):
ssh -o StrictHostKeyChecking=no $SSH_USER@$SSH_HOST

# Corrección: agregar el host a known_hosts antes de conectar
ssh-keyscan $SSH_HOST >> ~/.ssh/known_hosts
ssh $SSH_USER@$SSH_HOST ...
```

## Comparación con otros proyectos Muvin

> [!success]
> Este repositorio tiene una **postura de seguridad significativamente mejor** que `config-deploys` en cuanto a gestión de secretos: usa Vault + clave SSH en lugar de `sshpass` + contraseña en texto plano.

## Referencias

- [[deuda-tecnica]]
- [[hotspots]]

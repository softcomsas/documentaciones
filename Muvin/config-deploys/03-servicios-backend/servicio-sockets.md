# Servicio: Sockets (Node.js + Redis)

## Descripción

Servicio de comunicación en tiempo real de la plataforma Muvin. Corre como contenedor Docker gestionado con `docker-compose`. El pipeline lo redespliega ejecutando `docker-compose down && docker-compose up -d` en el servidor destino.

## Imagen Docker

```
registry.bcr.com.ar/muvinapp/sockets:{env}
```

## Configuración docker-compose en servidor

```yaml
# sockets/docker-compose.yml
version: '3'
services:
  socket-muvin:
    image: registry.bcr.com.ar/muvinapp/sockets:{env}
    ports:
      - "5000:5000"
    restart: always

  redis:
    image: redis:7.0.4-alpine
    restart: always
```

## Puerto expuesto

| Puerto | Servicio |
|--------|---------|
| `5000` | WebSocket / Socket.IO |

## Cómo se despliega

```bash
# En el servidor destino (/opt/sockets):
docker login registry.bcr.com.ar -u $USER -p $DEPLOY_TOKEN
docker pull registry.bcr.com.ar/muvinapp/sockets:{env}
docker-compose down
docker-compose up -d
```

> [!warning] Deuda técnica
> El bloque `only:` de los jobs de sockets está comentado en el pipeline, lo que puede causar deploys no intencionados. Ver [[deuda-tecnica]] ítem DT-09.
> 
> Además se usa `docker-compose` (CLI v1 deprecado) en lugar de `docker compose` (plugin v2). Ver DT-12.

## Ruta en servidor

```
/opt/sockets/
├── docker-compose.yml
└── .env (si existe, no versionado)
```

## Redis

- Imagen: `redis:7.0.4-alpine`
- Rol: broker de mensajes/caché para el servicio de sockets.
- Sin persistencia configurada — los datos se pierden al reiniciar el contenedor.

## Referencias

- [[modulo-deploy-sockets]]
- [[funcionalidad-deploy-automatico]]
- [[deuda-tecnica]] — DT-09, DT-12

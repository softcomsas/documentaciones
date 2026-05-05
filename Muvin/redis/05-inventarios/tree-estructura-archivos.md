# Estructura de Archivos — Redis Muvin

```
redis/
├── .github/
│   └── workflows/
│       ├── deploy-dev.yml        # Deploy automático en dev via GitHub Actions
│       └── sync-cap.yml          # Sincronización repo hacia cap
└── docker/
    └── docker-compose.yml        # Definición del servicio (uso local/referencia)
```

## Inventario de archivos

| Archivo | Líneas aprox. | Tipo | Descripción |
|---------|--------------|------|-------------|
| `.github/workflows/deploy-dev.yml` | ~55 | YAML / GitHub Actions | Pipeline de deploy en dev |
| `.github/workflows/sync-cap.yml` | ~20 | YAML / GitHub Actions | Sync de rama cap |
| `docker/docker-compose.yml` | ~18 | YAML / Docker Compose | Definición local del contenedor |

## Archivos notablemente ausentes

| Archivo | ¿Por qué falta? |
|---------|----------------|
| `Dockerfile` | Se usa imagen oficial sin customización |
| `redis.conf` | Configuración por defecto de Redis |
| `.env` | Variables manejadas por Vault y GitHub Secrets |
| `README.md` | No existe en el repositorio |
| Workflows para `uat` y `prd` | Ambientes no configurados aún |

## Referencias

- [[_indice-modulos]]

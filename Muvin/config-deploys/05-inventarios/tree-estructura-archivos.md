# Árbol de Estructura de Archivos — Config-Deploys Muvin

> **Fecha de análisis:** 2026-05-05

```
config-deploys/
├── .gitlab-ci.yml                        # 🔴 Pipeline CI/CD completo (1057 líneas, 4 ambientes)
├── .github/
│   └── workflows/
│       └── sync.yml                      # 🔄 Sincronización GitHub→GitLab
├── deploy_back.sh                        # 🧙 Script manual de deploy de API (git pull + yii)
├── deploy_front.sh                       # 🧙 Script manual de deploy de Frontend (npm build + rsync)
├── README.md                             # Instrucciones de variables GitLab y pasos de deploy
├── sockets/
│   └── docker-compose.yml               # 🔌 Stack de sockets: socket-muvin + Redis 7.0.4
├── conf/
│   └── mantenimiento/
│       └── mantenimiento.conf           # ⚙️ VirtualHost Apache para modo mantenimiento
└── versiones/
    └── README.md                        # ⚠️ Solo contiene "API DEV / API UAT" — sin valor actual
```

## Archivos NO versionados (inferidos / referenciados en scripts)

| Archivo/Path | Referenciado en | Propósito |
|--------------|----------------|-----------|
| `/opt/backups/muvinbackupdatabase` | `deploy_back.sh` | Script de backup de DB en el servidor |
| `/opt/deploy/api/` | `.gitlab-ci.yml` | Directorio staging para extracción de imagen API |
| `/opt/deploy/panel/` | `.gitlab-ci.yml` | Directorio staging para extracción de imagen FE |
| `/opt/sockets/` | `.gitlab-ci.yml` | Directorio de Docker Compose de sockets |
| `/opt/deploy/muvinapp-new-panel/` | `deploy_front.sh` | Clone del repo frontend para build manual |
| `/opt/backups/data/muinapp_PRE_DEPLOY.gz` | `.gitlab-ci.yml` | Backup MySQL pre-deploy |
| `/etc/apache2/sites-available/panel.muvinapp.com` | `.gitlab-ci.yml` | VirtualHost del panel en Apache |
| `/etc/apache2/sites-available/mantenimiento` | `.gitlab-ci.yml` | VirtualHost de mantenimiento en Apache |

## Notas

- 📦 **El archivo más crítico es `.gitlab-ci.yml`** — sin él no hay despliegues automatizados.
- ⚠️ **`versiones/README.md`** — solo contiene "API DEV\nAPI UAT". Sin valor documental real.
- ⚠️ **No hay archivos de tests** — el pipeline no ejecuta tests unitarios propios; los "unit tests" son comandos Yii2 de conexión.

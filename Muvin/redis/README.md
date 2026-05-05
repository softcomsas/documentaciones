# Redis — Muvin

> Documentación técnica del servicio Redis de la plataforma Muvin.

## Stack

| Componente | Versión |
|------------|---------|
| Redis | `7` (imagen oficial Docker Hub) |
| Docker | Engine ≥ 20.x |
| GitHub Actions | CI/CD de deploy y sync |
| HashiCorp Vault | Gestión de secretos SSH |

## Estructura del repositorio

```
redis/
├── .github/
│   └── workflows/
│       ├── deploy-dev.yml    # Deploy automático en ambiente dev
│       └── sync-cap.yml      # Sincronización repo a ambiente cap
└── docker/
    └── docker-compose.yml    # Definición local del servicio
```

## Secciones

- [[00-overview/vision-general|Visión General]]
- [[01-modulos/_indice-modulos|Módulos]]
- [[02-funcionalidades/_indice-funcionalidades|Funcionalidades]]
- [[03-servicios-backend/_indice-servicios|Servicios]]
- [[07-operacion-y-despliegue/requisitos-entorno|Operación y Despliegue]]
- [[08-riesgos-y-deuda-tecnica/hotspots|Riesgos y Deuda Técnica]]

## Links rápidos

- Registry: Docker Hub oficial (`redis:7`)
- Vault: `https://vault.alternativasinteligentes.com`
- Red Docker compartida: `muvin-net`
- Puerto: `6379`

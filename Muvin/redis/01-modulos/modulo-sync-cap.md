# Módulo: Sync Cap (GitHub Actions)

## Descripción

Workflow que sincroniza el repositorio GitHub hacia un repositorio espejo cuando se hace push a la rama `cap` (o se elimina la rama). Permite que el código de cap llegue a otro sistema de CI/CD (posiblemente GitLab).

## Archivo: `.github/workflows/sync-cap.yml`

```yaml
name: Sync DEV muvin-redis

on:
  push:
    branches:
      - cap
  delete:
    branches:
      - cap

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - uses: wangchucheng/git-repo-sync@v0.1.0
        with:
          target-url: ${{ secrets.TARGET_URL }}
          target-username: ${{ secrets.TARGET_USERNAME }}
          target-token: ${{ secrets.TARGET_TOKEN }}
```

## Secretos requeridos en GitHub

| Secret | Descripción |
|--------|-------------|
| `TARGET_URL` | URL del repositorio destino (probablemente GitLab) |
| `TARGET_USERNAME` | Usuario con permisos de push en el destino |
| `TARGET_TOKEN` | Token de acceso al repositorio destino |

## Comportamiento ante `delete`

> [!note]
> El workflow se dispara también con el evento `delete` sobre la rama `cap`. Esto significa que si se elimina la rama `cap` en GitHub, se sincroniza esa eliminación al repo destino (`--mirror`).

## Deuda técnica

> [!warning]
> Usa `actions/checkout@v2` (versión flotante, no anclada a SHA). Riesgo de supply chain si la acción es comprometida. Mismo patrón que en `config-deploys`.

## Referencias

- [[modulo-deploy-dev]]
- [[flujo-sync-cap]]

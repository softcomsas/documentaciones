# Funcionalidad: Deploy Automático por Push de Rama

## Descripción

Cada vez que se hace `git push` a una rama protegida (`dev`, `cap`, `uat`, `prd`), GitLab CI/CD dispara automáticamente el pipeline de deploy para el ambiente correspondiente.

## Trigger y mapeo de ramas

| Rama | Ambiente | Servidor |
|------|----------|----------|
| `dev` | Desarrollo | `$DEV_IP` |
| `cap` | Capacitación | `$CAP_IP` |
| `uat` | User Acceptance Testing | `$UAT_IP` |
| `prd` | Producción | `$PRD_IP` |

## Componentes desplegados en cada ejecución

1. **API Backend** — imagen Docker extraída del registry → rsync a `/var/www/html/api`
2. **Frontend Panel** — imagen Docker extraída del registry → rsync a `/var/www/html`
3. **Sockets** — `docker-compose down && docker-compose up -d` en `/opt/sockets`

## Stages del pipeline

```
1-images → 2-backup_database → 3-maintenance_on →
4-deploy_api + 4-deploy_fe + 4-deploy_sockets (paralelos) →
5-validate → 6-maintenance_off
```

## Restricciones

- Los jobs `only:` están configurados por rama.
- Los deploys a `cap`, `uat` y `prd` se disparan mediante `trigger` desde el pipeline del ambiente anterior (promoción entre ambientes).

## Configuración relevante

```yaml
# Fragmento .gitlab-ci.yml
4-deploy_api_dev:
  stage: 4-deploy_api
  only:
    - dev
  script:
    - docker run --rm -v /sync:/app registry.bcr.com.ar/muvinapp/muvinapp-new-api:dev sh -c "cp -r /app/. /sync/"
    - sshpass -p $DEV_PASS rsync -avz --delete /sync/ $DEV_USER@$DEV_IP:/var/www/html/api/
```

## Dependencias

- Registry BCR accesible con `$DEPLOY_TOKEN`
- Conectividad SSH al servidor destino
- Usuario con permisos `sudo` para `chmod` en el destino

## Referencias

- [[modulo-gitlab-ci]]
- [[flujo-nueva-imagen]]
- [[funcionalidad-backup-database]]

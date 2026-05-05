# Índice de Flujos Transversales — Config-Deploys Muvin

| # | Flujo | Descripción | Módulos involucrados | Archivo |
|---|-------|-------------|---------------------|---------|
| 1 | Deploy completo DEV | Ciclo completo: mantenimiento → backup → API → sockets → FE → restaurar | Todos | [[flujo-deploy-completo]] |
| 2 | Promoción DEV → CAP → PRD | Encadenamiento de ambientes via trigger | gitlab-ci | [[flujo-promocion-ambientes]] |
| 3 | Notificación de nueva imagen | Pipeline disparado por imagen nueva sin deploy completo | gitlab-ci, deploy-api/fe/sockets | [[flujo-nueva-imagen]] |
| 4 | Sync GitHub → GitLab → Pipeline | Cadena completa desde push en GitHub | github-sync, gitlab-ci | [[flujo-sync-github-gitlab]] |

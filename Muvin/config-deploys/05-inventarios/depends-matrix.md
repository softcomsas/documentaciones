# Matriz de Dependencias — Config-Deploys Muvin

> `✔` = el módulo **fila** depende del módulo **columna**
> `—` = sin dependencia directa

| | **gitlab-ci** | **deploy-api** | **deploy-fe** | **deploy-sockets** | **mantenimiento** | **scripts-manuales** | **github-sync** |
|---|---|---|---|---|---|---|---|
| **gitlab-ci** | — | ✔ | ✔ | ✔ | ✔ | — | — |
| **deploy-api** | — | — | — | — | — | — | — |
| **deploy-fe** | — | — | — | — | — | — | — |
| **deploy-sockets** | — | — | — | — | — | — | — |
| **mantenimiento** | — | — | — | — | — | — | — |
| **scripts-manuales** | — | — | — | — | — | — | — |
| **github-sync** | ✔ | — | — | — | — | — | — |

## Notas al pie

| Celda | Tipo de dependencia | Descripción |
|-------|---------------------|-------------|
| gitlab-ci → deploy-api | Orquestación (stage) | El pipeline invoca los jobs de deploy API como etapas hijas |
| gitlab-ci → deploy-fe | Orquestación (stage) | Ídem para el frontend |
| gitlab-ci → deploy-sockets | Orquestación (stage) | Ídem para el servicio de sockets |
| gitlab-ci → mantenimiento | Orquestación (stage) | Invoca los jobs de pre/post mantenimiento en cada ciclo |
| github-sync → gitlab-ci | Trigger indirecto | El sync de GitHub actualiza el repo en GitLab; los cambios en el `.gitlab-ci.yml` afectan el pipeline |

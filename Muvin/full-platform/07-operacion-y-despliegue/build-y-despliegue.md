# Build y Despliegue

> **Última revisión:** 2026-04-29

## Desarrollo Local

### Levantar todo el monorepo (shell + todos los MFEs)

```bash
# Instalar dependencias
pnpm install

# Levantar el shell con todos los microfrontends en modo federado
nx run main:serve
```

### Levantar un MFE individualmente

```bash
# Ejemplo: levantar solo logística
nx run mf-logistica:serve
```

> Los MFEs pueden correr en modo standalone. El `proxy.conf.json` del shell redirige las llamadas `/api/*` a los backends locales.

### Backends NestJS (descargas-app)

```bash
cd descargas-app/backend
cp .env-template .env   # completar con valores locales
# Instalar dependencias PHP/Node según corresponda
nx run descargas-backend:serve  # o el target definido en project.json
```

### Backends Yii2 (logística, oferta, documentación)

```bash
cd logistica-app/backend/api/source
composer install
cp local.env .env       # completar con credenciales locales
php yii serve           # o usar docker-compose
```

## Build de Producción

### Frontend (todos los MFEs)

```bash
# Script de build general
bash scripts/build-frontend.sh
```

### MFE individual

```bash
nx build <nombre-app> --configuration=production
# Ejemplo:
nx build descargas-app --configuration=production
```

> Los builds de producción usan `webpack.prod.config.js` en cada app.

### Docker

```bash
# Build imagen base frontend
docker build -f dockerfiles/frontend.base.dockerfile -t aisa-frontend-base .

# Build imagen de un MFE específico (con su docker config)
cd descargas-app/frontend
docker build -f docker/Dockerfile -t aisa-descargas-frontend .
```

## Pipeline CI/CD (GitLab)

> Ver `.gitlab-ci.yml` en la raíz del repositorio.

| Stage | Descripción |
|---|---|
| ⚠️ Stages pendientes de documentar | Ver `.gitlab-ci.yml` para stages actuales |

## Migraciones de Base de Datos

### Sequelize (NestJS backends)

```bash
cd descargas-app/backend
npx sequelize-cli db:migrate
```

> Migraciones ubicadas en `descargas-app/backend/migrations/` y la global en `migrations/` de la raíz.

### Yii2 (PHP backends)

```bash
cd logistica-app/backend/api/source
php yii migrate
```

## Rollback

### Sequelize

```bash
npx sequelize-cli db:migrate:undo  # Revierte la última migración
npx sequelize-cli db:migrate:undo:all  # Revierte todas
```

### Yii2

```bash
php yii migrate/down  # Revierte la última migración
```

## Creación de Nuevo Módulo

```bash
# Script automatizado para andamiaje de nuevo módulo
bash scripts/create-module.sh <nombre-modulo>
```

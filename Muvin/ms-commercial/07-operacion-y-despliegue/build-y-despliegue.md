# Build y Despliegue

**Última actualización:** 2026-04-27

## Local Development

### Setup Inicial

```bash
# 1. Clonar
git clone https://github.com/muvin-com/ms-commercial
cd ms-commercial

# 2. Dependencias
npm install

# 3. Setup Prisma
npx prisma generate
npx prisma migrate deploy

# 4. Correr en desarrollo
npm run dev
```

Servidor escucha en `localhost:4005`.

---

## Build para Producción

```bash
# Compilar TypeScript → JavaScript
npm run build

# Resultado: dist/
```

---

## Docker Build

```bash
# Build image
docker build -t ms-commercial:latest .

# Verificar
docker images | grep ms-commercial

# Correr localmente
docker run -p 4005:4005 \
  -e DATABASE_URL="mysql://..." \
  ms-commercial:latest
```

---

## Despliegue: CI/CD (GitHub Actions)

### Archivo: `.github/workflows/deploy-dev.yml`

Flujo automático:

```
Push a rama 'dev'
    ↓
Build Docker image
    ↓
Push a docker-registry.alternativasinteligentes.com
    ↓
SSH conectar a servidor
    ↓
Pull image nueva
    ↓
docker-compose down
    ↓
docker-compose up -d
    ↓
Verificar que corre en :4005
```

---

### Disparadores

| Evento | Rama | Acción |
|--------|------|--------|
| push | dev | Build + deploy dev |
| push | main | Build + deploy prod |
| pull_request | * | Run tests |

---

### Credenciales Requeridas

En GitHub (Settings → Secrets):

```
DOCKER_REGISTRY_URL
DOCKER_REGISTRY_USERNAME
DOCKER_REGISTRY_PASSWORD
DEPLOY_SERVER_HOST
DEPLOY_SERVER_USER
DEPLOY_SERVER_KEY (SSH private key)
```

---

## Despliegue Manual

Si CI/CD falla:

```bash
# 1. En tu máquina
docker build -t ms-commercial:v1.0.0 .

# 2. Tag para registry
docker tag ms-commercial:v1.0.0 \
  docker-registry.alternativasinteligentes.com/ms-commercial:v1.0.0

# 3. Push al registry
docker push docker-registry.alternativasinteligentes.com/ms-commercial:v1.0.0

# 4. En servidor (SSH)
ssh user@server

# 5. Pull y correr
docker-compose down
docker pull docker-registry.alternativasinteligentes.com/ms-commercial:v1.0.0
docker-compose up -d

# 6. Verificar
docker ps | grep ms-commercial
docker logs ms-commercial-1
```

---

## Verificar Deployment

```bash
# Local
npm run dev
# Server debe responder en localhost:4005

# Remoto (SSH)
curl http://localhost:4005
# O enviar RPC message:
# echo '{"pattern": "..."}'
```

---

## Rollback

Si el deployment falla:

```bash
# En servidor
docker-compose down
docker pull docker-registry.../ms-commercial:PREVIOUS_TAG
docker-compose up -d
```

---

## Logs

```bash
# Docker logs
docker logs ms-commercial-1 -f

# O en servidor
docker-compose logs -f ms-commercial
```

---

## Checklist Despliegue

- [ ] Tests pasan localmente (`npm test`)
- [ ] Build compila sin errores (`npm run build`)
- [ ] Docker image se construye (`docker build`)
- [ ] ENV vars configuradas en servidor
- [ ] DB está disponible
- [ ] Imágenes previas respaladas (backup)
- [ ] Deploy completado
- [ ] Logs limpios (sin errores)
- [ ] Health check pasa

---

## Documentos Relacionados

- [[requisitos-entorno|Requisitos de Entorno]] - Setup
- [[configuracion|Configuración]] - Variables


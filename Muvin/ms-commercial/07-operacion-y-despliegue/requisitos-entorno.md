# Requisitos de Entorno

**Última actualización:** 2026-04-27

## Local (Desarrollo)

### Hardware Mínimo
- CPU: 2+ cores
- RAM: 4GB+
- Storage: 2GB libre

### Software Requerido

| Software | Versión | Propósito |
|----------|---------|----------|
| **Node.js** | 20.x LTS | Runtime |
| **npm** | 10.x | Package manager |
| **MySQL** | 8.x | Base de datos |
| **Git** | 2.30+ | Control de versión |
| **Docker** (opt) | 20.x+ | Containerización |

### Instalación

```bash
# macOS
brew install node@20
brew install mysql

# Windows
# Descargar desde nodejs.org y mysql.com

# Linux (Ubuntu)
sudo apt update
sudo apt install nodejs npm mysql-server
```

---

## Desarrollo: MySQL Local

### Setup MySQL en Desarrollo

```bash
# Iniciar servidor
mysql.server start

# Crear DB
mysql -u root -p
> CREATE DATABASE dev_commercial;
> CREATE USER 'dev_user'@'localhost' IDENTIFIED BY 'SecurePass123';
> GRANT ALL PRIVILEGES ON dev_commercial.* TO 'dev_user'@'localhost';
> FLUSH PRIVILEGES;
```

### Connection String Local

```
DATABASE_URL="mysql://dev_user:SecurePass123@localhost:3306/dev_commercial"
```

---

## Producción (Azure MySQL)

### Servidor Externo

```
Host: muvinapp-preprod-db.mysql.database.azure.com
Database: dev_commercial
User: muvinapp_user
Port: 3306
```

### Connection String Producción

```
DATABASE_URL="mysql://muvinapp_user:PASSWORD@muvinapp-preprod-db.mysql.database.azure.com:3306/dev_commercial"
```

---

## Docker Requirements

### Si usas Docker Compose

```bash
# Instalación
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Verificar
docker --version
docker-compose --version
```

---

## Ports

| Servicio | Puerto | Protocolo |
|----------|--------|-----------|
| ms-commercial | 4005 | TCP (RPC) |
| MySQL | 3306 | TCP |
| Prisma Studio | 5555 | HTTP (dev only) |

---

## Variables de Entorno

Archivo `.env` (NO commitar):

```bash
# Microservice Config
COMMERCIAL_MICROSERVICE_HOST=localhost
COMMERCIAL_MICROSERVICE_PORT=4005
COMMERCIAL_MICROSERVICE_TRANSPORT=0

# Database
DATABASE_URL="mysql://dev_user:SecurePass123@localhost:3306/dev_commercial"

# (opcional)
NODE_ENV=development
LOG_LEVEL=debug
```

---

## Checklist Setup Local

- [ ] Node.js 20+ instalado
- [ ] npm 10+ instalado
- [ ] MySQL 8.x corriendo
- [ ] DB `dev_commercial` creada
- [ ] `.env` configurado
- [ ] `npm install` ejecutado
- [ ] `npx prisma generate` ejecutado
- [ ] `npm run dev` funciona

---

## Documentos Relacionados

- [[build-y-despliegue|Build y Despliegue]] - Cómo compilar
- [[configuracion|Configuración]] - Detalle de env vars
- [[../00-overview/stack-tecnologico|Stack Tecnológico]] - Versiones exactas


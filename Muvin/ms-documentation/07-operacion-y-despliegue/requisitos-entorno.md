---
tags: [operación, entorno, requisitos]
última-revisión: 2026-04-27
---

# Requisitos de entorno

## Entorno de desarrollo local

| Requisito | Versión mínima | Notas |
|-----------|---------------|-------|
| Node.js | 20 (LTS) | Recomendado usar `nvm` o `fnm` para gestión de versiones |
| npm | 10+ | Incluido con Node.js 20 |
| Docker | 20+ | Para levantar MySQL local con `docker-compose` |
| MySQL | 8.0 | Levantable vía `docker-compose` incluido |
| Git | 2.x | — |

## Variables de entorno requeridas

Crear un archivo `.env` en la raíz del proyecto copiando `.env-template`:

```bash
cp .env-template .env
```

Luego completar con los valores reales:

| Variable | Descripción | Ejemplo local |
|----------|-------------|---------------|
| `HOST` | Host donde escucha el microservicio | `localhost` |
| `PORT` | Puerto TCP | `4002` |
| `DATABASE_URL` | Cadena de conexión MySQL | `mysql://root:muvin@localhost:3306/db_commercial` |

## Entorno de producción (DEV)

| Aspecto | Detalle |
|---------|---------|
| Plataforma | Servidor con Docker |
| Red | Red Docker `muvin-net` |
| Puerto expuesto | `4002:4002` |
| Imagen | `docker-registry.alternativasinteligentes.com/muvin/muvin-ms-cpe:dev` |
| Secretos | Gestionados vía HashiCorp Vault (`vault.alternativasinteligentes.com`) |
| Variables de entorno | Inyectadas en tiempo de deploy desde Vault |

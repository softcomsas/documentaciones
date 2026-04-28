# Índice de Archivos de Datos

**Última actualización:** 2026-04-27

## Configuración (Config Files)

| Archivo | Ubicación | Propósito |
|---------|-----------|----------|
| .env | raíz | Variables de entorno (NO commitar) |
| .env.example | raíz | Template de .env |
| environments.ts | src/config/ | Validación Joi |
| transport.ts | src/config/ | Transport TCP config |
| nest-cli.json | raíz | CLI configuration |
| tsconfig.json | raíz | TypeScript config |
| jest.config.js | raíz | Testing config |

---

## Base de Datos

| Archivo | Ubicación | Propósito |
|---------|-----------|----------|
| schema.prisma | prisma/ | Definición de tablas |
| migrations/ | prisma/ | Historial de cambios |

---

## Docker & Deploy

| Archivo | Ubicación | Propósito |
|---------|-----------|----------|
| Dockerfile | raíz | Build image |
| docker-compose.yml | raíz | Compose para dev |
| deploy-dev.yml | .github/workflows/ | GitHub Actions deploy |

---

## Documentos Relacionados

- [[tree-estructura-archivos|Árbol de Archivos]] - Estructura completa
- [[../07-operacion-y-despliegue/configuracion|Configuración]] - Detalles


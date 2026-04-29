# Operación y Despliegue — muvin-api

> **Última revisión:** 2026-04-29

---

## Requisitos de entorno

| Requisito | Versión | Notas |
|-----------|---------|-------|
| Node.js | ≥ 18 LTS | Runtime principal |
| npm / pnpm | Cualquiera reciente | Gestor de paquetes |
| Docker | 20.x+ | Para despliegue containerizado |
| ms-legacy | Activo | Microservicio TCP requerido por LegacyModule |
| ms-logs | Activo | Microservicio TCP requerido por LogsModule |

---

## Variables de entorno

Crear un archivo `.env` en la raíz del proyecto:

```env
# Servidor
PORT=3000

# Microservicio Legacy
LEGACY_MICROSERVICE_HOST=localhost
LEGACY_MICROSERVICE_PORT=3001
LEGACY_MICROSERVICE_TRANSPORT=TCP
LEGACY_MICROSERVICE_SERVICE=LEGACY_SERVICE

# Microservicio Logs
LOGS_MICROSERVICE_HOST=localhost
LOGS_MICROSERVICE_PORT=3002
LOGS_MICROSERVICE_TRANSPORT=TCP
LOGS_MICROSERVICE_SERVICE=LOGS_SERVICE
```

> ⚠️ Las credenciales de `TemporaryModule` (`USER`, `PASSWORD`, URLs) están hardcodeadas en `src/modules/temporary/configuration.ts` y **no se leen de variables de entorno**. Ver [[deuda-tecnica]].

---

## Scripts disponibles

| Script | Comando | Descripción |
|--------|---------|-------------|
| Desarrollo | `npm run start:dev` | Modo watch con hot-reload |
| Debug | `npm run start:debug` | Watch + inspector Node.js |
| Producción | `npm run start:prod` | Ejecuta `dist/main.js` compilado |
| Build | `npm run build` | Compila TypeScript → `dist/` |
| Tests | `npm run test` | Jest (unit tests) |
| Tests coverage | `npm run test:cov` | Jest con reporte de cobertura |
| Lint | `npm run lint` | ESLint con auto-fix |

---

## Despliegue con Docker

```bash
# Build de la imagen
docker build -f docker/Dockerfile -t muvin-api .

# Ejecutar con variables de entorno
docker run -p 3000:3000 \
  -e PORT=3000 \
  -e LEGACY_MICROSERVICE_HOST=ms-legacy \
  -e LEGACY_MICROSERVICE_PORT=3001 \
  -e LOGS_MICROSERVICE_HOST=ms-logs \
  -e LOGS_MICROSERVICE_PORT=3002 \
  muvin-api

# Con docker-compose
cd docker/
docker-compose up -d
```

---

## Estructura de build

```
dist/
├── main.js           # Entry point compilado
├── module.js
└── modules/
    ├── graphql.js
    ├── rest.js
    └── ...
```

El comando `npm run build` usa `nest build` que delega en `tsc` con los paths configurados en `tsconfig.build.json`.

---

## URL de acceso

| Entorno | URL base | GraphQL |
|---------|----------|---------|
| Local | `http://localhost:3000/api` | `http://localhost:3000/api/graphql` |
| Docker | `http://localhost:{PORT}/api` | `http://localhost:{PORT}/api/graphql` |

---

## Logs

La función `LOG()` de `common/functions/` es el mecanismo de logging interno. Escribe a `stdout` y es usada en todos los módulos para registrar eventos de arranque y errores.

> ℹ️ No hay integración con sistemas de logging externos (Winston, Datadog, etc.).

---

## Monitoreo

> ⚠️ No existe endpoint de healthcheck ni integración con sistemas de monitoreo. Se recomienda agregar `GET /health` que verifique la conectividad con ms-legacy y ms-logs.

---

## Referencias

- [[stack-tecnologico]]
- [[arquitectura-general]]
- [[deuda-tecnica]]

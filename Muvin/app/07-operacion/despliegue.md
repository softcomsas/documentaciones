# Operación y Despliegue — muvin-app

## Docker

```dockerfile
# Dockerfile (multi-stage)
# Stage 1: build Angular
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Nginx
FROM nginx:alpine
COPY --from=builder /app/dist/ /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/conf.d/default.conf
```

## Nginx

`nginx.conf` en raíz del proyecto:
- Sirve el build de Angular (`/dist`)
- Redirige todas las rutas al `index.html` (SPA fallback)
- Proxy `/api/` → backend (ver `proxy.conf.json` para desarrollo local)

## proxy.conf.json (desarrollo)

```json
{
  "/api": {
    "target": "http://localhost:3000",
    "secure": false,
    "changeOrigin": true
  }
}
```

## Scripts disponibles

| Script | Comando | Descripción |
|---|---|---|
| `start` | `ng serve` | Desarrollo local con proxy |
| `build` | `ng build` | Build de producción |
| `watch` | `ng build --watch --configuration development` | Build continuo |
| `lint` | `ng lint` | Análisis estático ESLint |
| `i18n` | `ng extract-i18n ...` | Extrae strings para traducción |

## Variables de entorno

Archivo: `src/environments/development.ts`

```typescript
export const ENVIRONMENT = {
  api: 'http://localhost:3000',   // URL base del backend
};
```

Para producción se usa `environment.prod.ts` vía Angular build configurations.

## i18n

- Locale base: inglés (`messages.en.xlf`)
- Output path: `src/locale/`
- Strings marcados con `$localize` en rutas, breadcrumbs y labels

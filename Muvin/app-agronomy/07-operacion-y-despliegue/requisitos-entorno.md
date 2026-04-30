# Requisitos de Entorno y Despliegue — App Agronomy

> **Última revisión:** 2026-04-30

## Requisitos de entorno de desarrollo

| Herramienta | Versión recomendada | Propósito |
|-------------|---------------------|-----------|
| Node.js | ≥18.x | Runtime de build |
| pnpm | ≥8.x | Gestor de paquetes |
| Angular CLI | 16.x | CLI de desarrollo |
| Navegador moderno | Chrome/Firefox/Edge últimas versiones | Testing local |

## Variables de entorno

Configuradas en `src/environments/`:

| Variable | Descripción | Archivo |
|----------|-------------|---------|
| `apiURL` | URL base de la API REST | `environment.ts` / `environment.prod.ts` |

> [!warning] Configuración sensible
> El archivo `environment.prod.ts` contiene la URL de la API de producción. No debe incluirse en el control de versiones público.

## Comandos de desarrollo

```bash
# Instalar dependencias
pnpm install

# Servidor de desarrollo (puerto 4200)
pnpm start   # equivale a: ng serve --port 4200

# Build de producción
pnpm build   # equivale a: ng build

# Build en modo watch (desarrollo)
pnpm watch   # equivale a: ng build --watch --configuration development

# Tests unitarios
pnpm test    # equivale a: ng test
```

## Build de producción

El build utiliza una **configuración Webpack personalizada** (`webpack.prod.config.js`) sobre Angular CLI. Esto requiere que esté correctamente configurado en `angular.json` bajo `customWebpackConfig`.

El resultado del build se genera en `dist/`. El `Dockerfile` automatiza este proceso.

## Docker / Despliegue

```dockerfile
# El Dockerfile realiza:
# 1. Build de la app Angular en un contenedor Node
# 2. Copia los archivos compilados a un contenedor Nginx
# 3. Usa nginx/default.conf para servir la SPA
```

**Configuración Nginx:** `nginx/default.conf` debe incluir el fallback SPA (`try_files $uri /index.html`) para que el enrutamiento client-side funcione correctamente en recargas de página.

## PWA

La app está configurada como PWA:
- `ngsw-config.json`: define estrategias de caché para assets y API calls.
- `manifest.webmanifest`: nombre, íconos, colores de la app instalable.
- Se activa automáticamente en el build de producción (`ng build`).

> [!info] Service Worker en desarrollo
> En modo desarrollo (`ng serve`), el Service Worker **no está activo** por defecto para evitar conflictos con el hot-reload.

# Índice de Archivos de Datos — App Agronomy

> **Última revisión:** 2026-04-30

## Configuración y entorno

| Archivo | Propósito | Formato | Regenerable | Notas |
|---------|-----------|---------|-------------|-------|
| `src/environments/environment.ts` | Variables de entorno desarrollo (`apiURL`, etc.) | TypeScript | Manual | No commitear valores de producción |
| `src/environments/environment.prod.ts` | Variables de entorno producción | TypeScript | Manual | 🔒 Contiene URL de API de producción |
| `angular.json` | Config Angular CLI (build, test, serve) | JSON | Manual | Incluye referencia a webpack custom |
| `tsconfig.json` | Config TypeScript raíz | JSON | Manual | — |
| `tsconfig.app.json` | Config TypeScript para build | JSON | Manual | — |
| `webpack.config.js` | Config Webpack dev personalizada | JavaScript | Manual | ⚠️ Config custom sobre Angular CLI |
| `webpack.prod.config.js` | Config Webpack prod personalizada | JavaScript | Manual | ⚠️ Config custom sobre Angular CLI |
| `ngsw-config.json` | Config Service Worker PWA (estrategias de caché) | JSON | Manual | Define qué rutas/assets se cachean offline |
| `karma.conf.js` | Config tests unitarios | JavaScript | Manual | — |
| `nginx/default.conf` | Config Nginx para servir la SPA | Nginx conf | Manual | Maneja fallback SPA (`try_files`) |
| `Dockerfile` | Imagen Docker de producción | Dockerfile | Manual | Build + Nginx |

## Assets estáticos

| Directorio / Archivo | Contenido | Notas |
|---------------------|-----------|-------|
| `src/assets/` | Imágenes, íconos, traducciones i18n | Los archivos de i18n (`i18n/*.json`) son consumidos por `@ngx-translate` |
| `src/manifest.webmanifest` | Manifest PWA (nombre, íconos, colores) | — |
| `src/favicon.ico` | Ícono del browser | — |

## Archivos de base de datos / mocking

| Archivo | Propósito | Estado |
|---------|-----------|--------|
| `src/app/shared/inmemory-db/` | API en memoria para mocking (`angular-in-memory-web-api`) | 🚧 Verificar si está activo. Si lo está, intercepta las peticiones HTTP reales |

## Archivos de lock / dependencias

| Archivo | Propósito |
|---------|-----------|
| `pnpm-lock.yaml` | Lockfile exacto de dependencias (reproducibilidad del build) |
| `package.json` | Definición de dependencias y scripts npm |

# Configuración

> **Última revisión:** 2026-04-29

## Configuración Angular (Entornos)

| Archivo | Propósito | Cuándo se usa |
|---|---|---|
| `environments/environment.ts` | Variables de entorno de **desarrollo** | `nx serve` |
| `environments/environment.prod.ts` | Variables de entorno de **producción** | `nx build --configuration=production` |

## Configuración Nx (Monorepo)

| Archivo | Propósito |
|---|---|
| `nx.json` | Configuración global de Nx: caché de tareas, plugins, runners |
| `tsconfig.base.json` | Paths de TypeScript compartidos entre todas las apps (aliases de importación) |
| `jest.preset.js` | Preset Jest compartido para todas las apps |
| `tailwind.config.js` | Config raíz de Tailwind CSS |

## Configuración por App

Cada app tiene su propio directorio de configuración:

| Archivo | Descripción |
|---|---|
| `<app>/project.json` | Targets de Nx (serve, build, test, lint) para esa app |
| `<app>/tsconfig.json` | TypeScript config específica de la app |
| `<app>/webpack.config.js` | Webpack config de desarrollo (con Module Federation) |
| `<app>/webpack.prod.config.js` | Webpack config de producción |
| `<app>/module-federation.config.js` | Configuración de Module Federation (remotes/exposes) |

## Proxy de Desarrollo

`main/proxy.conf.json` — redirige las llamadas del shell Angular a los backends locales durante el desarrollo:

```json
{
  "/api/descargas": { "target": "http://localhost:<puerto-nestjs>" },
  "/api/logistica": { "target": "http://localhost:<puerto-yii2-logistica>" }
}
```

> ⚠️ Los puertos exactos y rutas del proxy requieren verificación en el archivo real.

## Configuración Sequelize (descargas-app)

| Archivo | Propósito |
|---|---|
| `descargas-app/backend/src/config/sequelize.module.ts` | Módulo NestJS que configura Sequelize con variables de entorno |
| `descargas-app/backend/config/` | Config de Sequelize-CLI para migraciones |

## Configuración Yii2 (logística / oferta / documentación)

| Archivo | Propósito |
|---|---|
| `*/backend/api/source/config/web.php` | Config principal Yii2 (componentes, módulos, URL rules) |
| `*/backend/api/source/config/db.php` | Configuración de base de datos |
| `*/backend/api/source/config/console.php` | Config de consola Yii2 (migraciones, comandos) |
| `*/backend/api/source/local.env` | Variables locales de entorno (⚠️ verificar que no esté en git) |

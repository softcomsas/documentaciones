# Stack Tecnológico

> **Última revisión:** 2026-04-29

## Frontend

| Tecnología | Versión | Propósito | Soporte Vendor | Riesgo |
|---|---|---|---|---|
| Angular | 16.2.x | Framework principal de todos los MFEs | 🟡 EOL Nov 2024 | 🟡 Requiere actualización a v17+ |
| @angular-architects/module-federation | 15.0.x | Implementación de Module Federation en Angular | 🟢 Vigente (compatible con v16) | 🟢 Bajo |
| Nx | 16.8.x / 16.10.x | Monorepo tooling, generadores, build cache | 🟡 Versión no latest (actual v19) | 🟡 Actualización pendiente |
| NGXS | 3.8.x | State management global | 🟢 Vigente | 🟢 Bajo |
| Angular Material | 16.2.x | Componentes UI Material Design | 🟡 Vinculado a Angular v16 | 🟡 Actualizar junto con Angular |
| Vex (theme) | Embebido en `@vex/` | Tema de UI para el shell y algunos MFEs | ⚠️ Pendiente de verificar versión | 🟡 Customización propia embebida |
| Ionic Angular | 7.7.x | Apps mobile (pedidos-mobile, superapp-mobile) | 🟢 Vigente | 🟢 Bajo |
| @ngx-translate | 14.x / 7.x | Internacionalización (i18n) | 🟢 Vigente | 🟢 Bajo |
| @ngxs/storage-plugin | 3.7.x | Persistencia de estado en localStorage | 🟢 Vigente | 🟢 Bajo |
| angular-calendar | 0.31.x | Componente de calendario | 🟢 Vigente | 🟢 Bajo |
| apexcharts | 3.37.x | Gráficos y visualizaciones | 🟢 Vigente | 🟢 Bajo |
| date-fns | 2.29.x | Utilidades de fechas | 🟡 v2 (actual v3) | 🟡 Migración a v3 recomendada |
| tailwindcss | Configurado en tailwind.config.js | Utilidades CSS | 🟢 Vigente | 🟢 Bajo |

## Backend NestJS (nuevos servicios)

| Tecnología | Versión | Propósito | Soporte Vendor | Riesgo |
|---|---|---|---|---|
| NestJS | 10.x | Framework backend TypeScript | 🟢 Vigente | 🟢 Bajo |
| @nestjs/sequelize | 10.x | ORM para acceso a base de datos | 🟢 Vigente | 🟢 Bajo |
| @nestjs/swagger | 7.3.x | Documentación automática de API | 🟢 Vigente | 🟢 Bajo |
| @nestjs/schedule | 4.x | Cron jobs y tareas programadas | 🟢 Vigente | 🟢 Bajo |
| @nestjs/axios | 3.x | Cliente HTTP | 🟢 Vigente | 🟢 Bajo |
| @nestjs/config | 3.x | Gestión de variables de entorno | 🟢 Vigente | 🟢 Bajo |
| class-validator | 0.14.x | Validación de DTOs | 🟢 Vigente | 🟢 Bajo |
| class-transformer | 0.5.x | Transformación de objetos | 🟢 Vigente | 🟢 Bajo |
| jsonwebtoken | 9.x | Verificación de JWT | 🟢 Vigente | 🟢 Bajo |
| exceljs | 4.4.x | Generación de reportes Excel | 🟢 Vigente | 🟢 Bajo |
| exiftool-vendored | 30.x | Procesamiento de metadatos de archivos | 🟢 Vigente | 🟡 Dependencia nativa |
| lodash | 4.17.x | Utilidades JS | 🟡 Mantenimiento mínimo | 🟡 Preferir métodos nativos ES2022+ |

## Backend Legacy (Yii2 PHP)

| Tecnología | Versión | Propósito | Soporte Vendor | Riesgo |
|---|---|---|---|---|
| Yii2 | 2.x (inferido) | Framework MVC PHP para logística, oferta y documentación | 🟡 Mantenimiento (Yii3 en desarrollo) | 🔴 Legacy activo, sin hoja de ruta de migración documentada |
| PHP | ⚠️ Pendiente de verificar | Runtime de Yii2 | ⚠️ Pendiente | 🔴 Si es PHP < 8.1, EOL |
| Composer | 2.x (inferido) | Gestión de dependencias PHP | 🟢 Vigente | 🟢 Bajo |
| Codeception | Configurado | Testing PHP | 🟢 Vigente | 🟢 Bajo |

## Infraestructura y Tooling

| Tecnología | Versión | Propósito | Soporte Vendor | Riesgo |
|---|---|---|---|---|
| Node.js | 18.16.0 | Runtime JS/TS | 🟡 LTS (EOL Apr 2025) | 🔴 Requiere actualizar a v20 LTS |
| pnpm | 8.8.0 | Gestor de paquetes | 🟢 Vigente | 🟢 Bajo |
| Docker | Presente en dockerfiles/ | Containerización | 🟢 Vigente | 🟢 Bajo |
| Nginx | nginx.conf en dockerfiles/ | Servidor estático para frontends | 🟢 Vigente | 🟢 Bajo |
| Webpack 5 | Vía @angular-architects/mf | Bundling y Module Federation | 🟢 Vigente | 🟢 Bajo |
| Jest | Configurado en jest.config.ts | Unit testing | 🟢 Vigente | 🟢 Bajo |
| ESLint | Configurado | Linting de TS/JS | 🟢 Vigente | 🟢 Bajo |
| Prettier | Configurado | Formateo de código | 🟢 Vigente | 🟢 Bajo |
| GitLab CI | .gitlab-ci.yml | Pipeline CI/CD | 🟢 Vigente | 🟢 Bajo |
| GitHub Actions | .github/ en algunos submódulos | CI alternativo | 🟢 Vigente | 🟡 Duplicidad con GitLab CI |

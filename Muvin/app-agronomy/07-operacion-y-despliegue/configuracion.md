# Configuración — App Agronomy

> **Última revisión:** 2026-04-30

## Archivos de configuración

### `src/environments/environment.ts` (desarrollo)

```typescript
export const environment = {
  production: false,
  apiURL: 'http://localhost:PORT/api/' // ⚠️ Pendiente verificar valor real
};
```

### `src/environments/environment.prod.ts` (producción)

```typescript
export const environment = {
  production: true,
  apiURL: '⚠️ Pendiente de verificar (no exponer en docs públicas)'
};
```

### `proxy.conf.json` (si existe)

⚠️ Verificar si existe un `proxy.conf.json` en la raíz para el servidor de desarrollo. Angular CLI permite configurar un proxy para redirigir llamadas a la API y evitar problemas de CORS en desarrollo.

## Configuración PWA (`ngsw-config.json`)

Define qué assets y rutas de API se cachean para uso offline. 🚧 Verificar las estrategias configuradas (freshness vs prefetch) para entender el comportamiento offline.

## Internacionalización

La app usa `@ngx-translate/core`. Los archivos de traducción deben estar en `src/assets/i18n/` (ej: `es.json`, `en.json`). 🚧 Verificar idiomas soportados.

## Roles del sistema

| Rol ID | Descripción | Acceso |
|--------|-------------|--------|
| `3` | Centro agroindustrial | Pedidos + Transportistas |
| `5` | Dador de carga | Solo sus propios pedidos |
| `11` | ⚠️ Pendiente de verificar | Pedidos + Transportistas |
| `16` | ⚠️ Pendiente de verificar | Pedidos + Transportistas |

> [!danger] Roles hardcodeados
> Los IDs de rol están hardcodeados en `CentroAuthGuard` y en servicios. Cualquier cambio en los IDs del backend requiere un deploy de frontend.

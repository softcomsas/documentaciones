---
tags: [operación, configuración, variables]
última-revisión: 2026-04-27
---

# Configuración

## Variables de entorno

La configuración del microservicio se gestiona **exclusivamente mediante variables de entorno**. No hay archivos de configuración estáticos con valores de producción.

### Variables requeridas

| Variable | Tipo | Descripción | Validación |
|----------|------|-------------|-----------|
| `HOST` | string | Host de escucha del servidor TCP | Obligatoria, no vacía |
| `PORT` | number | Puerto de escucha TCP | Obligatoria, número |
| `DATABASE_URL` | string | Cadena de conexión MySQL (formato Prisma) | Obligatoria, no vacía |

### Formato de DATABASE_URL

```
mysql://USUARIO:CONTRASEÑA@HOST:PUERTO/NOMBRE_DB
```

Ejemplo:
```
mysql://root:password@localhost:3306/muvin_cpe
```

## Configuración de transporte

El protocolo de transporte está fijado a **TCP** en `src/config/transport.ts`. No es configurable mediante variables de entorno — es una decisión de arquitectura.

## Comportamiento fail-fast

Si cualquier variable requerida falta o tiene tipo incorrecto al arrancar, la aplicación lanza un error inmediatamente con un mensaje claro indicando qué variable es inválida. El proceso termina antes de intentar conectar a la base de datos o iniciar el servidor TCP.

## Configuración del servidor TCP

| Parámetro | Fuente | Descripción |
|-----------|--------|-------------|
| `host` | `HOST` env var | Host de escucha (ej: `0.0.0.0` en producción) |
| `port` | `PORT` env var | Puerto TCP (default: `4002`) |
| `transport` | Fijo: `Transport.TCP` | Protocolo NestJS Microservices |

## Configuración Docker

En producción, el contenedor se ejecuta con:
- `NODE_ENV=production`
- `PORT=4002` (fijo en Dockerfile)
- Las demás variables se inyectan en tiempo de deploy desde HashiCorp Vault

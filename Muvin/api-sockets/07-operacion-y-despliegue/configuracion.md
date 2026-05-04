# Configuración

> [[requisitos-entorno]]

## Archivo `.env`

Crear `.env` en la raíz del proyecto (no versionado):

```dotenv
PORT=5000
REDIS_URL=redis://localhost:6379
```

## `global/environment.ts`

```typescript
export const SERVER_PORT: number = Number( process.env.PORT ) || 5000;
```

El puerto se lee de `process.env.PORT`. Si no está definido, usa `5000`.

## Redis URL formats

| Entorno | URL ejemplo |
|---------|-------------|
| Local sin auth | `redis://localhost:6379` |
| Local con auth | `redis://:mipassword@localhost:6379` |
| Docker service | `redis://redis:6379` |
| Redis Cloud | `redis://default:password@host.redis.cloud:6379` |

## `docker-compose.yml` recomendado

```yaml
version: '3.8'

services:
  socket-muvin:
    build: .
    container_name: socket-muvin
    ports:
      - "5000:5000"
    environment:
      - PORT=5000
      - REDIS_URL=redis://redis:6379
    depends_on:
      - redis

  redis:
    image: redis:7-alpine
    container_name: socket-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  redis_data:
```

## CORS

Configurado en `index.ts` con `origin: true, credentials: true`.  
Para restringir en producción:

```typescript
server.app.use( cors({
    origin: ['https://app.muvin.com', 'https://app-agronomy.muvin.com'],
    credentials: true
}));
```

## tsconfig.json (referencia)

La compilación TypeScript genera archivos en `dist/`. El entry point en producción es:

```bash
node dist/
# equivalente a: node dist/index.js
```

## Comandos de operación

| Comando | Descripción |
|---------|-------------|
| `npm install` | Instalar dependencias |
| `tsc` | Compilar TypeScript → `dist/` |
| `node dist/` | Iniciar servidor compilado |
| `curl http://localhost:5000/borrar-usuarios` | Resetear Redis al arranque |
| `curl http://localhost:5000/mensajes` | Healthcheck |

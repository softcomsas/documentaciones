# Requisitos de Entorno

> [[_indice-operacion]] | [[configuracion]]

## Software base

| Componente | Versión requerida | Notas |
|------------|-------------------|-------|
| Node.js | >= 16.x | Docker usa `current-alpine3.16` |
| npm | >= 8.x | Incluido con Node.js |
| TypeScript | >= 4.x (global) | Para compilar fuera de Docker |
| Redis | >= 6.x | Compatible con `redis` npm v4 |

## Instalación local

```bash
# Instalar dependencias
npm install

# Instalar TypeScript globalmente (si no está)
npm install -g typescript

# Compilar
tsc

# Arrancar (requiere Redis corriendo)
node dist/
```

## Variables de entorno requeridas

| Variable | Descripción | Default |
|----------|-------------|---------|
| `PORT` | Puerto HTTP/WebSocket | `5000` |
| `REDIS_URL` | URL de conexión Redis | _(obligatorio)_ |

## Redis

Redis debe estar corriendo y accesible antes de arrancar el servidor.

```bash
# Iniciar Redis local (Docker)
docker run -d -p 6379:6379 redis:alpine

# Verificar conexión
redis-cli ping   # → PONG
```

## Docker

```bash
# Build
docker build -t api-sockets .

# Run (requiere Redis externo)
docker run -p 5000:5000 \
  -e PORT=5000 \
  -e REDIS_URL=redis://host.docker.internal:6379 \
  api-sockets
```

## Inicialización de Redis

Al primera arranque, ejecutar para limpiar estado previo:

```bash
curl http://localhost:5000/borrar-usuarios
```

Esto inicializa `listaUsuarios = []` en Redis.

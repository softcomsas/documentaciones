# Recomendaciones de Modernización — api-sockets

> [[deuda-tecnica]] | [[hotspots]]

## Fase 1 — Correcciones inmediatas (sin refactor)

### R-01: Corregir bug de inicialización Redis

```typescript
// Antes (bugueado):
if(u != null || u == "[]") { ... }

// Después:
const users = await clientRedis.get("listaUsuarios");
const listUsers: any[] = users ? JSON.parse(users) : [];
listUsers.push({ id: cliente.id, id_persona: payload.id_persona, name: payload.nombre });
await clientRedis.set("listaUsuarios", JSON.stringify(listUsers));
```

### R-02: Limpiar in-memory en disconnect

```typescript
// Descomentar en desconectar():
const user = usuariosConectados.borrarUsuario( cliente.id );
```

### R-03: Eliminar cliente Redis muerto en server.ts

Eliminar las líneas:
```typescript
this.clientRedist = createClient();
//client.connect();
```
Y el campo `public clientRedis: any;`

### R-04: Restringir CORS a orígenes conocidos

```typescript
server.app.use( cors({
    origin: (process.env.CORS_ORIGINS || '').split(','),
    credentials: true
}));
```

### R-05: Corregir typos

- `clientRedist` → `clientRedis` (si se mantiene)
- `"No esiste el chofer."` → `"No existe el chofer."`

---

## Fase 2 — Seguridad y robustez

### R-06: Autenticar endpoints REST con API Key

```typescript
const apiKeyMiddleware = (req, res, next) => {
    if (req.headers['x-api-key'] !== process.env.API_KEY) {
        return res.status(401).json({ ok: false, error: 'Unauthorized' });
    }
    next();
};
router.use(apiKeyMiddleware);
```

### R-07: Rate limiting con express-rate-limit

```bash
npm install express-rate-limit
```
```typescript
import rateLimit from 'express-rate-limit';
const limiter = rateLimit({ windowMs: 60_000, max: 100 });
server.app.use(limiter);
```

### R-08: Manejo de error Redis caído

```typescript
clientRedis.on('error', (err) => {
    console.error('Redis error:', err);
    // No crashear el proceso
});
```

### R-09: Validar `id_persona` contra JWT

Al recibir `configurar-usuario`, verificar que el `id_persona` del payload coincide con el del token JWT que el cliente envió en el handshake Socket.IO:

```typescript
// En handshake:
io.use((socket, next) => {
    const token = socket.handshake.auth.token;
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    socket.data.id_persona = decoded.id_persona;
    next();
});
```

---

## Fase 3 — Arquitectura

### R-10: Migrar a Socket.IO v4

Socket.IO v4 requiere clientes v4 también. Cambios principales:
- `socketIO(httpServer)` → `new Server(httpServer)`
- Tipado mejorado con TypeScript
- Mejores herramientas de debugging

### R-11: Unificar almacenamiento en Redis

Eliminar `UsuariosLista` in-memory y almacenar todos los campos en Redis (hash o JSON extendido):

```
HSET usuario:<socket.id> id_persona 5 nombre "Carlos" id_chofer 42 consultas false
SADD consultas <socket.id>  ← set de consultores
```

Esto hace al servidor **stateless** y permite escalar horizontalmente.

### R-12: Usar NestJS Gateway (si el stack NestJS se adopta)

Migrar a `@nestjs/websockets` + `@nestjs/platform-socket.io` para alinear con `api` (NestJS). Permite guards, pipes y decoradores nativos de NestJS.

---

## Resumen de esfuerzo

| Fase | Items | Esfuerzo | Impacto |
|------|-------|----------|---------|
| 1 — Correcciones inmediatas | R-01 a R-05 | 0.5–1 día | 🔴 Crítico |
| 2 — Seguridad | R-06 a R-09 | 2–4 días | 🔴 Alto |
| 3 — Arquitectura | R-10 a R-12 | 2–4 semanas | 🟡 Estratégico |

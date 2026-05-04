# Inventario de Seguridad — api-sockets

> [[tree-estructura-archivos]] | [[../08-riesgos-y-deuda-tecnica/hotspots]]

## Hallazgos

### SEC-01 — Sin autenticación en ningún endpoint REST

**Severidad:** 🔴 Crítica  
**Archivo:** `routes/router.ts`

Todos los endpoints (`/notificacion`, `/consulta`, `/chofer-tienes-viaje`, etc.) son accesibles sin token ni credenciales. Cualquier proceso en la red puede:
- Enviar notificaciones a cualquier usuario
- Limpiar la lista de usuarios conectados
- Disparar eventos a cualquier chofer

---

### SEC-02 — CORS completamente abierto

**Severidad:** 🔴 Alta  
**Archivo:** `index.ts`, `routes/router.ts`

```typescript
server.app.use( cors({ origin: true, credentials: true }) );
router.use(cors());
```

`origin: true` acepta cualquier origen. Con `credentials: true` permite cookies cross-origin desde cualquier dominio.

---

### SEC-03 — Sin validación del payload de `configurar-usuario`

**Severidad:** 🟠 Media  
**Archivo:** `sockets/socket.ts`

El cliente declara su propio `id_persona`. No hay verificación de que ese `id_persona` corresponda al JWT del usuario. Cualquier cliente puede suplantar a cualquier `id_persona`.

---

### SEC-04 — Sin rate limiting

**Severidad:** 🟠 Media  
**Archivos:** `routes/router.ts`, `sockets/socket.ts`

No hay límite de conexiones WebSocket ni de llamadas REST. Vulnerable a:
- Flood de conexiones Socket.IO
- Spam de notificaciones via POST /notificacion

---

### SEC-05 — `REDIS_URL` en variable de entorno sin cifrado

**Severidad:** 🟡 Baja  
**Archivos:** `sockets/socket.ts`, `routes/router.ts`

Si `REDIS_URL` incluye credenciales Redis (`redis://:password@host:6379`), se expone en logs de proceso o variables de entorno del contenedor.

---

### SEC-06 — `docker-compose.yml.example` sin Redis con contraseña

**Severidad:** 🟡 Baja  
**Archivo:** `docker-compose.yml.example`

El ejemplo no incluye servicio Redis ni contraseña. Un deploy descuidado usará Redis sin auth.

---

## Resumen

| ID | Hallazgo | Severidad | Esfuerzo fix |
|----|----------|-----------|--------------|
| SEC-01 | Sin auth en REST | 🔴 Crítica | Medio |
| SEC-02 | CORS totalmente abierto | 🔴 Alta | Bajo |
| SEC-03 | Sin validación id_persona | 🟠 Media | Medio |
| SEC-04 | Sin rate limiting | 🟠 Media | Bajo |
| SEC-05 | Redis URL en env | 🟡 Baja | Bajo |
| SEC-06 | Docker sin Redis auth | 🟡 Baja | Bajo |

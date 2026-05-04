# Hotspots de Código — api-sockets

> [[_indice-riesgos]] | [[deuda-tecnica]]

## H-01 — `sockets/socket.ts` · doble almacenamiento inconsistente

**Riesgo:** 🔴 Alto

El mismo dato (usuarios conectados) vive en dos lugares con semánticas distintas:
- **Redis:** `{id, id_persona, name}` — actualizado en connect/disconnect
- **In-memory:** `Usuario` completo — actualizado en connect, **NO limpiado** en disconnect

**Consecuencia:** Tras múltiples conexiones/desconexiones, `usuariosConectados` crece indefinidamente. Los métodos `getChofer()` y `getConsultas()` pueden devolver usuarios ya desconectados.

---

## H-02 — `sockets/socket.ts` · Bug lógico en inicialización Redis

**Riesgo:** 🔴 Alto

```typescript
if(u != null || u == "[]" ) {
    // push
} else {
    // inicializar
}
```

La condición `u != null || u == "[]"` es ambigua. Si Redis nunca fue inicializado, `u = null` → la lista se parsea como `null` → `listUsers.push(...)` lanza `TypeError: Cannot read properties of null`.

**Fix:** `GET /borrar-usuarios` antes de arrancar, o inicializar en el constructor del servidor.

---

## H-03 — `classes/server.ts` · Redis creado pero no conectado

**Riesgo:** 🟠 Medio

```typescript
this.clientRedist = createClient();
//client.connect();   ← COMENTADO
```

Typo en nombre (`clientRedist`) + `.connect()` comentado → el cliente Redis de `server.ts` nunca se conecta y no es usado por nadie. Es código muerto que crea confusión.

---

## H-04 — `routes/router.ts` · Sin autenticación

**Riesgo:** 🔴 Crítico

Cualquier proceso con acceso a la red puede:
- `POST /notificacion` → spam a cualquier usuario
- `GET /borrar-usuarios` → eliminar todos los usuarios conectados en Redis (DoS)
- `POST /consulta` → inundar de consultas a los operadores

---

## H-05 — `sockets/socket.ts` · `configurar-usuario` sin validación de identidad

**Riesgo:** 🟠 Medio

```typescript
cliente.on('configurar-usuario', async (payload: { id_persona: number, nombre: string, ... }) => {
    // Se confía ciegamente en payload.id_persona
```

Un cliente malicioso puede declarar cualquier `id_persona` y recibir notificaciones destinadas a esa persona.

---

## Resumen

| ID | Archivo | Severidad | Fix estimado |
|----|---------|-----------|--------------|
| H-01 | `sockets/socket.ts` | 🔴 Alto | Unificar a un solo store |
| H-02 | `sockets/socket.ts` | 🔴 Alto | Corregir condición + inicializar Redis |
| H-03 | `classes/server.ts` | 🟠 Medio | Eliminar código muerto |
| H-04 | `routes/router.ts` | 🔴 Crítico | Middleware de auth |
| H-05 | `sockets/socket.ts` | 🟠 Medio | Validar contra JWT |

# Módulo: Entidades de Usuario

> [[_indice-modulos]] | Archivos: `classes/usuario.ts`, `classes/usuarios-lista.ts`

## `Usuario` — Modelo de presencia

Representa un cliente WebSocket conectado.

### Propiedades

| Propiedad | Tipo | Default | Descripción |
|-----------|------|---------|-------------|
| `id` | `string` | _(socket.id)_ | ID único de la conexión Socket.IO |
| `nombre` | `string` | `'sin-nombre'` | Nombre del usuario (se establece en `configurar-usuario`) |
| `sala` | `string` | `'sin-sala'` | Sala/room (campo presente pero no usado actualmente) |
| `id_chofer` | `number?` | `undefined` | ID del chofer si aplica |
| `id_centro` | `number?` | `undefined` | ID del centro de descarga si aplica |
| `consultas` | `boolean?` | `false` | Si `true`, recibe eventos `nueva-consulta` |

---

## `UsuariosLista` — Colección in-memory

Array en memoria de `Usuario[]` con operaciones de gestión.

### Métodos

| Método | Parámetros | Retorna | Descripción |
|--------|------------|---------|-------------|
| `agregar(usuario)` | `Usuario` | `Usuario` | Agrega al array |
| `actualizar(id, payload)` | `string, {nombre, id_chofer?, id_centro?, consultas}` | `void` | Actualiza nombre y campos del usuario |
| `getLista()` | — | `Usuario[]` | Lista filtrada: excluye `nombre === 'sin-nombre'` |
| `getUsuario(id)` | `string` | `Usuario \| undefined` | Busca por `socket.id` |
| `getChofer(id_chofer)` | `number` | `Usuario \| undefined` | Busca por `id_chofer` |
| `getConsultas()` | — | `Usuario[]` | Todos los usuarios con `consultas === true` |
| `getUsuariosEnSala(sala)` | `string` | `Usuario[]` | Filtra por sala (no usado actualmente) |
| `borrarUsuario(id)` | `string` | `Usuario \| undefined` | Elimina y devuelve el usuario |

## Diferencia in-memory vs Redis

| Aspecto | `UsuariosLista` (in-memory) | Redis `listaUsuarios` |
|---------|----------------------------|----------------------|
| Persistencia | ❌ Se pierde al reiniciar | ✅ Persiste |
| Campos almacenados | Todos (`id_chofer`, `id_centro`, `consultas`, etc.) | Solo `{id, id_persona, name}` |
| Usado por | `obtenerUsuarios`, `getChofer`, `getConsultas` | `notificacion`, `desconectar`, `/usuarios` REST |
| Sincronización al desconectar | ⚠️ Comentado (no se borra) | ✅ Se borra |

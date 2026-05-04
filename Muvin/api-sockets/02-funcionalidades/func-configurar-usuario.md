# F-02: Configuración de Usuario

> [[_indice-funcionalidades]] | Módulo: [[modulo-socket]]

## Evento Socket.IO

**Escucha:** `configurar-usuario`  
**Emite:** `configurar-usuario` (al solicitante) + `usuario-conectado` (broadcast)

## Payload del cliente

```typescript
{
    id_persona: number,   // ID de negocio en Muvin (obligatorio)
    nombre: string,       // Nombre visible
    id_chofer?: number,   // Si es chofer
    id_centro?: number,   // Si opera en un centro
    consultas: boolean    // True si debe recibir nuevas-consultas
}
```

## Comportamiento paso a paso

1. Lee `listaUsuarios` de Redis (`GET listaUsuarios`)
2. Parsea el JSON
3. Agrega `{id: cliente.id, id_persona, name: nombre}` al array
4. Escribe el array en Redis (`SET listaUsuarios`)
5. Emite `configurar-usuario` al cliente con la lista completa
6. Actualiza `usuariosConectados` in-memory (agrega `id_chofer`, `id_centro`, `consultas`)
7. Broadcast `usuario-conectado` a todos los clientes con el payload original

## Respuesta emitida al cliente

```typescript
// Evento: 'configurar-usuario'
[
    { id: "abc123", id_persona: 1, name: "Juan" },
    { id: "def456", id_persona: 2, name: "Pedro" },
    // ... todos los usuarios en Redis
]
```

## Broadcast a todos

```typescript
// Evento: 'usuario-conectado'
{ id_persona: 5, nombre: "Carlos", id_chofer: 10, id_centro: 3, consultas: false }
```

## Bug de condición lógica

```typescript
// Código actual (bugueado):
if(u != null || u == "[]" ) {
    // push al array
} else {
    // inicializar con primer usuario
}
```

La condición `u != null || u == "[]"` es siempre `true` cuando `u = null` porque `null != null` es `false`... espera, `u != null` cuando `u = null` es `false`, pero `u == "[]"` cuando `u = null` es `false`. Por lo tanto si `u = null`, la condición es `false` y entra al `else`. Sin embargo si Redis devuelve `"null"` como string podría causar problemas. El comportamiento real depende del estado inicial de Redis.

> ⚠️ Para garantizar inicialización correcta, ejecutar `GET /borrar-usuarios` antes del primer uso.

# F-09: Lista de Usuarios Activos

> [[_indice-funcionalidades]] | Módulo: [[modulo-socket]], [[modulo-router]]

## Vía Socket.IO

**Evento escuchado:** `obtener-usuarios`  
**Emite:** `usuarios-activos` **solo al solicitante** (`io.to(cliente.id)`)

```json
// Payload de 'usuarios-activos':
[
    { "id": "abc123", "nombre": "Juan", "sala": "sin-sala", "id_chofer": null, "id_centro": 3, "consultas": false },
    { "id": "def456", "nombre": "Carlos", "sala": "sin-sala", "id_chofer": 42, "id_centro": null, "consultas": false }
]
```

Filtra usuarios con `nombre === 'sin-nombre'` (aún no configurados).

---

## Vía REST — Lista desde Redis

**Endpoint:** `GET /usuarios`

Devuelve el array almacenado en Redis (`listaUsuarios`). Contiene solo `{id, id_persona, name}`.

```json
[
    { "id": "abc123", "id_persona": 1, "name": "Juan" },
    { "id": "def456", "id_persona": 5, "name": "Carlos" }
]
```

---

## Vía REST — Lista in-memory

**Endpoint:** `GET /usuarios/detalle`

Devuelve el objeto completo de `UsuariosLista.getLista()` (con todos los campos del `Usuario`).

```json
{
    "ok": true,
    "clientes": [
        { "id": "abc123", "nombre": "Juan", "sala": "sin-sala", "id_chofer": null, "id_centro": 3, "consultas": false }
    ]
}
```

---

## Diferencia entre los tres métodos

| Método | Fuente | Campos | Persistente |
|--------|--------|--------|-------------|
| Socket `obtener-usuarios` | In-memory | Completo | No |
| REST `/usuarios` | Redis | Solo `{id, id_persona, name}` | Sí |
| REST `/usuarios/detalle` | In-memory | Completo | No |

---

## Endpoint de mantenimiento

**`GET /borrar-usuarios`** — Resetea Redis: `SET listaUsuarios "[]"`  
Útil para limpiar el estado tras un reinicio del servidor.

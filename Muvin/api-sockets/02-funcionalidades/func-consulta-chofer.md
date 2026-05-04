# F-06 / F-07 / F-08: Consultas y Comunicación con Choferes

> [[_indice-funcionalidades]] | Módulo: [[modulo-router]]

## Contexto

El sistema Muvin tiene operadores logísticos ("consultores") que monitorean consultas de choferes. Los choferes (`id_chofer`) usan la app para comunicarse en tiempo real.

---

## F-06 — Enviar consulta a consultores

**Endpoint:** `POST /consulta`  
**Body:** libre (cualquier JSON — se reenvía íntegro)

### Comportamiento
1. Obtiene la lista de usuarios con `consultas === true` desde `usuariosConectados` (in-memory)
2. Emite `nueva-consulta` con el body completo a **cada** usuario consultor

```bash
curl -X POST http://api-sockets:5000/consulta \
  -H "Content-Type: application/json" \
  -d '{"id_chofer":42,"pregunta":"¿Cuál es el turno para maíz?"}'
```

**Evento emitido:** `nueva-consulta` → payload = req.body completo

---

## F-07 — Responder consulta a chofer

**Endpoint:** `POST /chofer-respuesta-consulta/:id_chofer`  
**Param:** `id_chofer` (number)  
**Body:** libre

### Comportamiento
1. Busca el chofer en `usuariosConectados` por `id_chofer`
2. Si no existe → `{ ok: false, mensaje: 'No esiste el chofer.' }` (⚠️ typo en código)
3. Emite `respuesta-consulta` con el body al socket del chofer

---

## F-08 — Notificar viaje disponible

**Endpoint:** `GET /chofer-tienes-viaje/:id_chofer`  
**Param:** `id_chofer` (number)

### Comportamiento
1. Busca el chofer en `usuariosConectados`
2. Si no existe → `{ ok: false }`
3. Emite `tienes-viaje: true` al socket del chofer

---

## Endpoint adicional: POST /chofer/:id_chofer

Emite `mensaje-privado` con payload hardcodeado `{mensaje:'consulta respuesta'}`.  
Parece ser un endpoint de prueba/debug.

---

## Resumen de eventos por chofer

| Endpoint llamante | Evento emitido al chofer | Payload |
|-------------------|--------------------------|---------|
| `POST /chofer-respuesta-consulta/:id` | `respuesta-consulta` | req.body |
| `GET /chofer-tienes-viaje/:id` | `tienes-viaje` | `true` |
| `POST /chofer/:id` | `mensaje-privado` | `{mensaje:'consulta respuesta'}` |

## Limitación importante

Estos endpoints buscan el chofer en `usuariosConectados` **in-memory**, NO en Redis. Si el proceso Node reinició, la lista in-memory está vacía y **todos los choferes aparecerán como no conectados** aunque estén conectados con `configurar-usuario` (que sí está en Redis).

# Glosario — api-sockets

> [[vision-general]]

| Término | Definición |
|---------|------------|
| **Socket.IO** | Librería de WebSocket con fallback a long-polling. v2 usa namespaces y rooms. |
| **socket.id** | Identificador único de conexión generado por Socket.IO. Cambia en cada reconexión. |
| **id_persona** | Identificador de persona en el sistema Muvin (clave de negocio, no socket.id). |
| **id_chofer** | Identificador de chofer en la plataforma logística de Muvin. |
| **id_centro** | Identificador de centro de acopio/descarga. |
| **listaUsuarios** | Clave en Redis que almacena un array JSON `[{id, id_persona, name}]`. |
| **UsuariosLista** | Clase TypeScript — lista en memoria de `Usuario[]` con operaciones CRUD. |
| **Usuario** | Entidad de presencia: `{id, nombre, sala, id_chofer, id_centro, consultas}`. |
| **consultas** | Flag booleano en `Usuario`. Si `true`, el usuario recibe eventos `nueva-consulta`. |
| **broadcast** | Envío de un evento a TODOS los clientes conectados (`io.emit`). |
| **mensaje privado** | Envío a un socket específico (`io.in(id).emit` o `io.to(id).emit`). |
| **configurar-usuario** | Evento Socket.IO emitido por el cliente al conectarse para registrar su identidad. |
| **nueva-consulta** | Evento emitido a todos los usuarios con `consultas=true`. |
| **respuesta-consulta** | Evento emitido al socket del chofer como respuesta a su consulta. |
| **tienes-viaje** | Evento emitido al chofer indicando que tiene un viaje asignado. |
| **mensaje-privado** | Evento genérico de mensaje con payload `{to, title, body, type}`. |
| **Redis URL** | Cadena de conexión Redis. Env: `REDIS_URL`. Ejemplo: `redis://localhost:6379`. |
| **Singleton** | Patrón de diseño en `Server`: sólo existe una instancia accesible via `Server.instance`. |

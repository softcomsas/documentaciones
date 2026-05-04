# Índice de Funcionalidades — api-sockets

> [[../README]]

## Funcionalidades implementadas

| ID | Nombre | Tipo | Descripción | Detalle |
|----|--------|------|-------------|---------|
| F-01 | Conexión de cliente | Socket.IO event | Registro automático al conectar | [[func-conexion-cliente]] |
| F-02 | Configuración de usuario | Socket.IO event | El cliente declara su identidad | [[func-configurar-usuario]] |
| F-03 | Broadcast de mensaje | Socket.IO event + REST | Mensaje a todos los conectados | [[func-mensaje-broadcast]] |
| F-04 | Mensaje privado por socket ID | REST | Mensaje a un socket específico | [[func-mensaje-privado]] |
| F-05 | Notificación por id_persona | REST | Notificación a usuarios por ID de negocio | [[func-notificacion]] |
| F-06 | Consulta a choferes | REST | Envío de consulta a operadores logísticos | [[func-consulta-chofer]] |
| F-07 | Respuesta a consulta de chofer | REST | Envío de respuesta al chofer consultante | [[func-consulta-chofer]] |
| F-08 | Notificación de viaje disponible | REST | Aviso de viaje asignado a chofer | [[func-consulta-chofer]] |
| F-09 | Lista de usuarios activos | Socket.IO event + REST | Presencia de usuarios conectados | [[func-lista-usuarios]] |
| F-10 | Desconexión de cliente | Socket.IO event | Limpieza al desconectar | [[func-desconexion]] |

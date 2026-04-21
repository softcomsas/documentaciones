# Flujo: Bot Chofer (WhatsApp)

> **Última revisión:** 2026-04-21
> **Ver también:** [[modulo-bot]], [[flujo-alta-cupo]], [[servicio-notificaciones]]

---

## Descripción

El **Bot de WhatsApp** permite a los choferes operar el sistema (ver cupos disponibles, tomar un cupo, consultar estado del viaje) sin necesidad de la app móvil, a través de mensajes de texto en WhatsApp.

---

## Actores

| Actor | Rol |
|-------|-----|
| Chofer | Usuario final del bot |
| WhatsApp Business API | Canal de comunicación |
| ChatBotController | Procesador de mensajes entrantes |
| MessengerController | Envío de mensajes de respuesta |
| Cupo | Entidad sobre la que opera |

---

## Flujo conversacional principal

```mermaid
stateDiagram-v2
    [*] --> Inicio: Chofer envía mensaje
    Inicio --> Identificacion: Bot pide CUIT
    Identificacion --> MenuPrincipal: CUIT válido
    Identificacion --> ErrorCuit: CUIT no encontrado

    MenuPrincipal --> VerCupos: "1. Ver cupos"
    MenuPrincipal --> MiViaje: "2. Mi viaje"
    MenuPrincipal --> Ayuda: "3. Ayuda"

    VerCupos --> ListaCupos: Listar disponibles
    ListaCupos --> ConfirmarToma: Chofer elige cupo
    ConfirmarToma --> CupoTomado: Confirma "SI"
    ConfirmarToma --> MenuPrincipal: Cancela "NO"
    CupoTomado --> [*]: Bot confirma y cierra

    MiViaje --> EstadoViaje: Mostrar estado actual
    EstadoViaje --> [*]

    ErrorCuit --> Inicio: Reintentar
```

---

## Flujo técnico de procesamiento de mensaje

```mermaid
sequenceDiagram
    participant WA as WhatsApp Business API
    participant BOT as ChatBotController (webhook)
    participant DB as MySQL
    participant MSG as MessengerController
    participant WA2 as WhatsApp (envío)

    WA->>BOT: POST /bot/webhook { from, body, ... }
    BOT->>DB: Buscar sesión del número
    DB-->>BOT: Estado conversacional actual
    BOT->>BOT: Procesar intención del mensaje
    BOT->>DB: Actualizar estado sesión
    BOT->>MSG: enviarMensaje(numero, respuesta)
    MSG->>WA2: POST /whatsapp/messages { to, text }
    WA2-->>MSG: 200 OK
    BOT-->>WA: 200 OK (ACK webhook)
```

---

## Configuración del bot

Los parámetros del bot se gestionan via `ParametrosChatController`:

| Parámetro | Propósito |
|-----------|-----------|
| Mensaje de bienvenida | Texto inicial del bot |
| Tiempo de sesión | Timeout de conversación |
| Número WhatsApp | Número origen del bot |
| Token API | Auth hacia WhatsApp Business |
| Productos disponibles | Qué granos puede operar via bot |

---

## Limitaciones conocidas

> [!warning] Sin persistencia de estado robusta
> El estado conversacional se guarda en BD, lo que puede ser lento para conversaciones con muchos turnos. Un Redis o sistema de sesiones más eficiente sería ideal.

> [!note] Dependencia de WhatsApp Business API
> Si cambia el proveedor de WhatsApp (ej: de Infobip a Twilio), requiere modificar `MessengerController` y la configuración de webhooks.

# Flujo: Notificación Gmail End-to-End

> **Proyecto:** `muvin-ms-integrations`
> **Revisión:** 2026-04-21
> **Alcance:** Desde que llega un email a Gmail hasta que el job queda en Redis

---

## Descripción

Este flujo cubre el camino completo de una notificación de email: desde que Google genera el evento de Pub/Sub hasta que el job queda disponible en Redis para que un worker externo lo procese. Atraviesa múltiples sistemas: Google Cloud, el ecosistema Muvin y este microservicio.

---

## Diagrama de secuencia completo

```mermaid
sequenceDiagram
    actor USER as Remitente
    participant GMAIL as Gmail (Google)
    participant PUBSUB as Google Pub/Sub
    participant MS_CALLER as MS Caller<br/>(externo, Muvin)
    participant GC as GmailController<br/>ms-integrations
    participant GS as GmailService<br/>ms-integrations
    participant GMAIL_API as Gmail API<br/>(Google REST)
    participant LABEL_CACHE as Labels Cache<br/>(in-memory)
    participant DB as MySQL<br/>db_integrations
    participant REDIS as Redis<br/>(Bull queue)
    participant WORKER as Worker<br/>(externo, Muvin)

    USER->>GMAIL: Envía email a cuenta@dominio.com
    GMAIL->>PUBSUB: Notificación push<br/>{emailAddress, historyId}
    PUBSUB->>MS_CALLER: HTTP POST (webhook Pub/Sub)
    MS_CALLER->>GC: TCP: integrations.email.notification<br/>{email: "cuenta@dominio.com", history: 12345}

    GC->>GS: notification({email, history})

    GS->>GS: Busca sesión en _sessions[]<br/>por email

    alt Sesión no encontrada
        GS-->>GC: return (void)
        Note over GC,MS_CALLER: Sin respuesta al caller
    else Sesión encontrada
        GS->>GMAIL_API: users.history.list<br/>{userId:'me', startHistoryId: session.history}
        GMAIL_API-->>GS: {history[], historyId}

        alt Sin cambios en history
            GS-->>GC: return (void)
        else Con cambios
            loop Por cada HistoryRecord con messagesAdded
                GS->>GMAIL_API: users.messages.get<br/>{id: messageId, format:'metadata', fields:'id,labelIds'}
                GMAIL_API-->>GS: {id, labelIds[]}

                GS->>LABEL_CACHE: filter(_list, labelIds)

                alt Ningún label coincide
                    Note over GS: Mensaje ignorado
                else Al menos un label coincide
                    GS->>DB: gmail_messages.create<br/>{label: matchingLabels[0].id, message: messageId}
                    DB-->>GS: {id, label, status: PENDING}
                    GS->>REDIS: queue.add('email.notification', job)<br/>job = {id, label, message, auth}
                    Note over REDIS,WORKER: Worker externo<br/>consume el job async
                end
            end

            GS->>DB: gmail_accounts.update<br/>{id, history: nuevoHistoryId}
            GS->>GS: session.history = nuevoHistoryId
        end
    end

    WORKER->>REDIS: Consume job 'email.notification'
    Note over WORKER,DB: Worker actualiza gmail_messages.status<br/>a SUCCESS, ERROR o TIMEOUT
```

---

## Puntos de integración externos

| Sistema | Protocolo | Dirección | Descripción |
|---|---|---|---|
| Google Pub/Sub | HTTP Push | Entrada (indirecta vía MS Caller) | Notifica que hay emails nuevos |
| Gmail API | REST/JWT | Salida | Consulta historial y metadata |
| Redis / Bull | TCP | Salida | Encola jobs para worker |
| MySQL | TCP | Bidireccional | Lee config, escribe mensajes |
| MS Caller (externo) | TCP NestJS | Entrada | Reenvía evento Pub/Sub |
| Worker (externo) | Redis/Bull | Consume | Procesa jobs encolados |

---

## Tiempos y latencias esperadas

| Etapa | Latencia esperada | Notas |
|---|---|---|
| Gmail → Pub/Sub | Segundos | Controlado por Google |
| Pub/Sub → MS Caller | Segundos | Depende de infraestructura Muvin |
| MS Caller → ms-integrations (TCP) | <100ms | Red interna |
| history.list (Gmail API) | 200-500ms | Depende de Google |
| messages.get (Gmail API) | 100-300ms por mensaje | Por cada mensaje nuevo |
| DB write + queue.add | <50ms | Local |

---

## Puntos de falla

| Punto | Impacto | Manejo actual |
|---|---|---|
| Gmail API no disponible | 🔴 Mensajes no procesados | ⚠️ Sin retry ni circuit breaker |
| Redis no disponible | 🔴 Jobs no encolados | ⚠️ Bull puede reintentar, pero sin configuración documentada |
| MySQL no disponible | 🔴 Sin persistencia ni procesamiento | ⚠️ Sin manejo explícito |
| Watch expirado | 🔴 Sin notificaciones | ⚠️ Sin renovación automática |
| Bootstrap fallido | 🔴 Servicio inoperante silenciosamente | ⚠️ Log pero sin health check expuesto |

---

## Ver también

- [[gmail-bootstrap]]
- [[gmail-notification]]
- [[gmail-api-endpoints]]
- [[hotspots]]
- [[deuda-tecnica]]

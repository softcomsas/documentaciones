# Funcionalidad: Procesamiento de Notificación de Email

> **Módulo:** [[modulo-gmail]]
> **Ruta UI / Endpoint de entrada:** `MessagePattern('integrations.email.notification')`
> **Tipo:** 🔌 Integración / Proceso automático

---

## Descripción funcional

Cuando un email nuevo llega a una bandeja de entrada monitoreada, Google Pub/Sub notifica al ecosistema Muvin. Un microservicio externo (el "caller") reenvía esa notificación a este microservicio vía TCP con el patrón `integrations.email.notification`. El servicio entonces:
1. Verifica que la cuenta que recibió el email esté activa en memoria.
2. Consulta el historial de Gmail para identificar qué mensajes son nuevos.
3. Filtra los mensajes según las labels configuradas en la base de datos.
4. Persiste el mensaje en `gmail_messages` con estado `PENDING`.
5. Encola un job en Bull/Redis para que un worker externo lo procese.

---

## Precondiciones

- El bootstrap ([[gmail-bootstrap]]) debe haber completado exitosamente.
- La cuenta que recibe la notificación debe estar en `_sessions` (activa).
- El `historyId` de la sesión debe estar actualizado para evitar procesar mensajes duplicados.
- Las labels configuradas en `gmail_labels` deben coincidir con las labels del mensaje en Gmail.

---

## Flujo principal

```mermaid
flowchart TD
    A([TCP: integrations.email.notification\n{email, history}]) --> B[GmailController.notification]
    B --> C[GmailService.notification]
    C --> D{¿Sesión activa\npara ese email?}
    D -->|No| E[LOG: Account not found\nreturn — sin efecto]
    D -->|Sí| F[gmail.users.history.list\nstartHistoryId = session.history]
    F --> G{¿history tiene\ncambios?}
    G -->|No o vacío| H[LOG: No history\nreturn — sin efecto]
    G -->|Sí| I[Iterar cada item del history]
    I --> J{¿item.messagesAdded\nexiste?}
    J -->|No| K[continue — siguiente item]
    J -->|Sí| L[Iterar messagesAdded]
    L --> M{¿messageId\npresente?}
    M -->|No| N[continue]
    M -->|Sí| O[gmail.users.messages.get\nformat=metadata, fields=id,labelIds]
    O --> P{¿labelIds\ntiene datos?}
    P -->|No| Q[continue]
    P -->|Sí| R{¿Algún labelId\nmatch en _list cache?}
    R -->|No| S[continue — mensaje ignorado]
    R -->|Sí| T[gmail_messages.create\nlabelId, messageId]
    T --> U[queue.add — job email.notification]
    U --> V[Continúa siguiente mensaje]
    V --> I
    I -->|Fin del loop| W{¿historyId cambió?}
    W -->|Sí| X[gmail_accounts.updateHistory\nnuevo historyId]
    X --> Y[session.history = nuevoId]
    Y --> Z([Fin])
    W -->|No| Z
```

---

## Flujos alternativos / excepciones

| Escenario | Comportamiento |
|---|---|
| Email de cuenta desconocida | Log y return inmediato sin error |
| Sin cambios en historial | Log y return inmediato |
| Mensaje sin labels | Se ignora, continúa con el siguiente |
| Label de Gmail no registrada en DB | Se ignora, continúa con el siguiente |
| Error en Gmail API | ⚠️ No manejado explícitamente — propagará excepción no capturada |

---

## Validaciones de negocio

| Validación | Acción | Ubicación en código |
|---|---|---|
| `session === undefined` | Return sin procesar | `src/modules/gmail/service.ts:notification` |
| `history === undefined \|\| length === 0` | Return sin procesar | `src/modules/gmail/service.ts:notification` |
| `!item.messagesAdded` | Skip item | `src/modules/gmail/service.ts:notification` |
| `!messageId` | Skip mensaje | `src/modules/gmail/service.ts:notification` |
| `!labelIds \|\| length === 0` | Skip mensaje | `src/modules/gmail/service.ts:notification` |
| `matchingLabels.length > 0` | Solo procesa el **primer** label que matchea | `src/modules/gmail/service.ts:notification` |

---

## Servicios backend invocados

| Paso | Recurso Gmail API | Propósito | Payload resumido | Respuesta resumida |
|---|---|---|---|---|
| 1 | `users.history.list` (GET) | Obtener cambios desde historyId | `{userId:'me', startHistoryId}` | `{history[], historyId}` |
| 2 | `users.messages.get` (GET) | Obtener metadata de mensaje | `{userId:'me', id, format:'metadata', fields:'id,labelIds'}` | `{id, labelIds[]}` |

---

## Datos que lee/escribe

- **Lee:** `_sessions` (en memoria), `_labels.list` (en memoria desde `gmail_labels` DB)
- **Escribe:** [[entidad-gmail-messages]] (nuevo registro con status `PENDING`), [[entidad-gmail-accounts]] (actualiza `history`)
- **Encola:** Job en Redis con payload `{id, label, message, auth: {email, key, scopes, domain, local}}`

---

## Payload del job encolado

```json
{
  "id": 123,
  "label": 5,
  "message": "18abcd1234ef5678",
  "auth": {
    "email": "service-account@project.iam.gserviceaccount.com",
    "key": "-----BEGIN RSA PRIVATE KEY-----\n...",
    "scopes": ["https://www.googleapis.com/auth/gmail.readonly"],
    "domain": "empresa.com",
    "local": "ventas"
  }
}
```

> [!danger] Credenciales en payload de cola
> El job encolado en Redis incluye la **private key del service account** en el campo `auth.key`. Cualquier consumidor del bus Redis con acceso a la cola `email` puede leer estas credenciales. Ver [[security-inventory]].

---

## Componentes involucrados

- `GmailController.notification()` — `src/modules/gmail/controller.ts`
- `GmailService.notification()` — `src/modules/gmail/service.ts`
- `GmailLabelsRepository._list` (cache) — `src/core/repositories/gmail-labels.ts`
- `GmailMessagesRepository.create()` — `src/core/repositories/gmail-messages.ts`
- `GmailAccountsRepository.updateHistory()` — `src/core/repositories/gmail-accounts.ts`
- `QueueService.add()` — `src/core/services/queue.ts`

---

## Riesgos específicos

- 🔴 **Credenciales en cola Redis**: la private key del service account se incluye en el payload del job. Ver [[security-inventory]].
- ⚠️ Solo se procesa el **primer label** que matchea (`matchingLabels[0]`). Si un mensaje tiene múltiples labels configuradas, solo se encola por la primera. Puede ser intencional pero no está documentado en código.
- ⚠️ El `notification()` devuelve `void` al caller TCP. Si el caller espera respuesta, el patrón debería ser `@MessagePattern` con retorno explícito (o `@EventPattern` para fire-and-forget).
- ⚠️ Si la Gmail API falla durante `history.list` o `messages.get`, la excepción no es capturada dentro de `notification()`, se propagará hacia NestJS sin manejo controlado.

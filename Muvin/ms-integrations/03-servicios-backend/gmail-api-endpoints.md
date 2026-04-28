# Gmail API — Endpoints Consumidos

> **Proyecto:** `muvin-ms-integrations`
> **API:** Gmail API v1 (`googleapis` SDK v166)
> **Autenticación:** JWT (Service Account con domain-wide delegation)
> **Revisión:** 2026-04-21

---

## Autenticación

Todos los requests a Gmail API se autentican usando JWT de service account con **Domain-Wide Delegation (DWD)**. Cada cuenta se impersona mediante el campo `subject` del JWT.

```typescript
// Construcción del JWT por cuenta (src/modules/gmail/service.ts:_jwt)
const jwt = new google.auth.JWT({
  email: serviceAccountEmail,
  key: privateKey,         // PEM key del service account
  scopes: scopesArray,     // ej: ['https://www.googleapis.com/auth/gmail.readonly']
  subject: `${local}@${domain}`,  // cuenta a impersonar
});
```

> [!info] Domain-Wide Delegation
> El service account debe tener DWD habilitado en Google Workspace Admin Console para poder impersonar cuentas del dominio.

---

## `POST users.watch` {#users-watch}

**Propósito:** Suscribir una cuenta de Gmail a notificaciones push vía Google Pub/Sub.
**Autenticación:** JWT por cuenta
**Consumido por:** [[gmail-bootstrap]]

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|---|---|---|---|---|
| `userId` | path | string | ✅ | Siempre `'me'` (hardcodeado) |
| `topicName` | requestBody | string | ✅ | Nombre del topic Pub/Sub (= `gmail_credentials.project`) |

### Request (SDK)

```typescript
gmail.users.watch({
  userId: 'me',
  requestBody: { topicName: this._token.project }
});
```

### Respuesta

| Campo | Tipo | Descripción |
|---|---|---|
| `historyId` | string | ID de historial desde donde empezar a escuchar |
| `expiration` | string | Timestamp de expiración del watch (ms desde epoch, como string) |

### Efectos secundarios

- Actualiza `gmail_accounts.watch`, `gmail_accounts.history`, `gmail_accounts.expiration` en la base de datos.
- En caso de error: setea `watch=false`, `history=null`, `expiration=null`.

> [!warning] Expiración del watch
> Los watches de Gmail expiran aproximadamente **cada 7 días**. No existe lógica de renovación automática en este microservicio. Si el watch expira, las notificaciones dejarán de llegar. Ver [[deuda-tecnica]].

---

## `GET users.history.list` {#users-history-list}

**Propósito:** Obtener los cambios en el buzón desde un `historyId` previo.
**Autenticación:** JWT por cuenta
**Consumido por:** [[gmail-notification]]

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|---|---|---|---|---|
| `userId` | path | string | ✅ | Siempre `'me'` |
| `startHistoryId` | query | string | ✅ | ID desde donde buscar cambios |

### Request (SDK)

```typescript
session.gmail.users.history.list({
  userId: 'me',
  startHistoryId: session.history
});
```

### Respuesta

| Campo | Tipo | Descripción |
|---|---|---|
| `history` | `HistoryRecord[]` | Lista de cambios. Puede incluir `messagesAdded`, `messagesDeleted`, etc. |
| `historyId` | string | Nuevo ID de historial actual |

### Efectos secundarios

- Si `historyId` cambió, actualiza `gmail_accounts.history` en DB y en memoria.

---

## `GET users.messages.get` {#users-messages-get}

**Propósito:** Obtener la metadata de un mensaje específico para identificar sus labels.
**Autenticación:** JWT por cuenta
**Consumido por:** [[gmail-notification]]

### Parámetros

| Nombre | Ubicación | Tipo | Obligatorio | Descripción |
|---|---|---|---|---|
| `userId` | path | string | ✅ | Siempre `'me'` |
| `id` | path | string | ✅ | ID del mensaje de Gmail |
| `format` | query | string | ✅ | `'metadata'` (minimiza datos) |
| `fields` | query | string | ✅ | `'id,labelIds'` (reduce payload) |

### Request (SDK)

```typescript
session.gmail.users.messages.get({
  userId: 'me',
  id: messageId,
  format: 'metadata',
  fields: 'id,labelIds'
});
```

### Respuesta

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | string | ID del mensaje |
| `labelIds` | `string[]` | IDs de labels asignadas al mensaje |

### Efectos secundarios

- Si algún `labelId` coincide con un label registrado en `gmail_labels`, se crea un registro en `gmail_messages` y se encola un job.

---

## Ver también

- [[gmail-bootstrap]]
- [[gmail-notification]]
- [[entidad-gmail-credentials]]
- [[security-inventory]]

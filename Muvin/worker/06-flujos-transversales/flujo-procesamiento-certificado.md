# Flujo: Procesamiento de Certificado de Transferencia de Granos

> **Módulos involucrados:** [[modulo-email]], [[modulo-services]], [[modulo-config]]
> **Disparador:** Job Bull en cola `email`, proceso `email.pdf`
> **Estado del flujo:** 🔴 Incompleto — el resultado no se persiste

---

## Descripción

Flujo de extremo a extremo del procesamiento de un certificado de transferencia de depósito de granos. Comienza cuando el API principal de Muvin detecta un correo de Google Workspace con un PDF adjunto, y termina (actualmente) con el resultado impreso en `console.log()`.

---

## Actores del flujo

| Actor | Sistema | Rol |
|-------|---------|-----|
| API Muvin | Sistema externo (productor) | Publica el job con `messageId` y credenciales |
| Redis | Bull broker | Transporta el job |
| Worker NestJS | Este proceso | Procesa el job |
| Gmail API v1 | Google Cloud | Provee el mensaje y los adjuntos |
| pdf-parse | Librería local | Extrae texto del PDF |

---

## Diagrama de secuencia completo

```mermaid
sequenceDiagram
    participant API as API Muvin (externo)
    participant REDIS as Redis Bull
    participant PROC as EmailProcessor
    participant JWT as google.auth.JWT
    participant GMAIL as Gmail API v1
    participant PDF as PdfParserService
    participant RT as rt.ts (extractores)

    API->>REDIS: bull.add('email.pdf', IJobEmailPdf)
    Note over REDIS: Job encolado en cola 'email'
    
    REDIS->>PROC: handleMail(Job<IJobEmailPdf>)
    Note over PROC: Desestructura messageId, auth

    PROC->>JWT: new google.auth.JWT(email, key, scopes, subject)
    JWT-->>PROC: Auth.JWT
    PROC->>GMAIL: google.gmail({ version:'v1', auth: jwt })
    GMAIL-->>PROC: GmailClient

    PROC->>GMAIL: messages.get({ userId:'me', id: messageId, format:'full' })
    GMAIL-->>PROC: Schema$Message (con payload MIME)

    alt message es undefined
        PROC->>PROC: return (silencioso — job exitoso vacío)
    end

    alt payload es undefined
        PROC->>PROC: return (silencioso — job exitoso vacío)
    end

    PROC->>RT: extractPartsFn(message.payload)
    RT-->>PROC: Schema$MessagePart[] (árbol aplanado)

    PROC->>RT: getAttachmentsFn(parts)
    RT-->>PROC: IRTFile[] (solo PDFs con attachmentId)

    alt files.length === 0
        PROC->>PROC: return (silencioso — job exitoso vacío)
    end

    loop Por cada archivo PDF
        PROC->>GMAIL: messages.attachments.get({ userId:'me', messageId, id: file.id })
        GMAIL-->>PROC: { data: string (base64) }

        alt res.data.data no existe
            PROC->>PROC: continue (salta este archivo)
        end

        PROC->>PDF: base64toText(res.data.data)
        PDF-->>PROC: TextResult { text: string }

        PROC->>RT: extractAndValidateTextFn(text)
        RT-->>PROC: { success, data: ITransferencia | null, errors }

        PROC->>PROC: console.log(data, name)
        Note over PROC: 🔴 DATO DESCARTADO — sin persistencia
    end

    Note over PROC: Job completado (éxito en Bull)
```

---

## Estados posibles del job

| Estado Bull | Condición | Comportamiento |
|------------|-----------|---------------|
| `completed` | Flujo normal (con o sin resultados) | Job marcado como exitoso aunque no procesó nada |
| `completed` | `message === undefined` | Return silencioso — éxito vacío |
| `completed` | `payload === undefined` | Return silencioso — éxito vacío |
| `completed` | `files.length === 0` | Return silencioso — éxito vacío |
| `failed` | Cualquier excepción no capturada (Gmail API error, pdf-parse error) | `throw err` → Bull marca como failed |

---

## Diagrama de flujo de datos

```mermaid
flowchart LR
    J[("Job payload\nIJobEmailPdf")] --> A["messageId\nauth"]
    A --> B["Schema$Message\n(Gmail API)"]
    B --> C["Schema$MessagePart[]\n(partes MIME)"]
    C --> D["IRTFile[]\n(PDFs)"]
    D --> E["base64 string\n(Gmail Attachment API)"]
    E --> F["TextResult.text\n(pdf-parse)"]
    F --> G["ITransferencia\n(extraído y validado)"]
    G --> H["console.log\n🔴 DESCARTADO"]
```

---

## Puntos de falla críticos

| # | Punto | Impacto | Causa probable |
|---|-------|---------|---------------|
| 1 | `messages.get()` 401/403 | Job failed + reintentos Bull | JWT inválido, DWD no configurado |
| 2 | `pdf-parse` falla | Job failed | PDF corrupto, cifrado, o escaneado sin OCR |
| 3 | `extractAndValidateTextFn` retorna `success: false` | 🔴 **Silencioso** — el job termina en `completed` | PDF de cultivo no soportado, cosecha futura |
| 4 | `console.log` en lugar de persistencia | 🔴 **Datos perdidos** — diseño incompleto | El worker no tiene cliente BD ni publisher de cola configurado |

---

## Lo que falta para completar el flujo

```mermaid
flowchart TD
    ACTUAL["Estado actual:\nconsole.log(data, name)"]
    N1["Opción A: Publicar job a cola interna\nBull.add('internal.notification', data)"]
    N2["Opción B: Llamada HTTP al API Muvin\nPATCH /transferencias/:id"]
    N3["Opción C: Escribir directamente a BD\n(requiere módulo TypeORM/Prisma)"]

    ACTUAL --> N1
    ACTUAL --> N2
    ACTUAL --> N3
```

Ver [[deuda-tecnica]] y [[recomendaciones-modernizacion]] para la recomendación.

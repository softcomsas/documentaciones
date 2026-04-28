# Funcionalidad: Validar Clave API

> **Módulo:** [[modulo-auth]]
> **CMD:** `auth.validate.key`
> **Tipo:** Seguridad / RPC send

## Descripción funcional

Valida que una clave API junto con su firma y timestamp sean legítimas. Es el punto central de autenticación del ecosistema — los consumidores (API Gateway, otros microservicios) invocan este comando antes de procesar cualquier request entrante de terceros.

## Flujo principal

```mermaid
flowchart TD
    A([Consumer envía TCP: auth.validate.key]) --> B[Recibe: key + signature + timestamp]
    B --> C[Buscar key en BD]
    C --> D{¿Key existe y active=true?}
    D -->|No| ERR1[Error: clave inválida o inactiva]
    D -->|Sí| E[Validar timestamp\n¿dentro de ventana aceptable?]
    E --> F{¿Timestamp válido?}
    F -->|No| ERR2[Error: request expirado]
    F -->|Sí| G[Recalcular firma con secret]
    G --> H{¿Firma coincide?}
    H -->|No| ERR3[Error: firma inválida]
    H -->|Sí| OK[Retorna IAuth\n{company, key}]
    OK --> Z([Respuesta TCP al consumer])
    ERR1 --> Z
    ERR2 --> Z
    ERR3 --> Z
```

> [!warning] Flujo esperado según contrato — handler no implementado.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| `key` | `string` | Sí | Clave API pública |
| `signature` | `string` | Sí | Firma generada por `auth.generate.signature` |
| `timestamp` | `number` | Sí | Unix timestamp del momento del request |

## Respuesta esperada

```typescript
{ success: true, data: IAuth }
// IAuth: { company: ICompany, key: IKey }
```

## Riesgos

- 🔴 Función crítica de seguridad — cualquier bug permite acceso no autorizado al ecosistema.
- 🔒 La ventana de validez del timestamp no está definida en el contrato — riesgo de replay attack.
- ⚠️ Sin rate limiting definido a nivel de contrato — susceptible a fuerza bruta.

## Datos que lee/escribe

- **Lee:** [[entidad-key]], [[entidad-company]]

## Archivos fuente relevantes

- `src/contracts/auth/interfaces/validation.ts` (interfaz `validate-key`)
- `src/contracts/auth/schema.ts` (IAuth)
- `src/common/cmd/interfaces/auth.ts`

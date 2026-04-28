# Funcionalidad: Generar Firma

> **Módulo:** [[modulo-auth]]
> **CMD:** `auth.generate.signature`
> **Tipo:** Seguridad / RPC send

## Descripción funcional

Genera una firma criptográfica para un request dado. Los consumidores que necesitan autenticar sus llamadas deben primero obtener una firma mediante este comando, luego incluirla junto con el timestamp en el request final que será validado por `auth.validate.key`.

## Flujo principal

```mermaid
flowchart TD
    A([Consumer envía TCP: auth.generate.signature]) --> B[Handler recibe payload\n{key, timestamp, ...data}]
    B --> C[Buscar secret de la key en BD]
    C --> D[Calcular HMAC/hash\nusando secret + payload + timestamp]
    D --> E[Retorna signature\n{signature: string}]
    E --> F([Respuesta TCP al consumer])
```

> [!warning] Flujo esperado según contrato — handler no implementado. El algoritmo de firma (HMAC-SHA256 u otro) es ⚠️ Pendiente de verificar en implementación.

## Payload de entrada

| Campo | Tipo | Obligatorio | Descripción |
|-------|------|-------------|-------------|
| ⚠️ Pendiente de verificar | — | — | Ver `src/contracts/auth/interfaces/validation.ts` |

## Respuesta esperada

```typescript
{ success: true, data: { signature: string } }
```

## Riesgos

- 🔒 El algoritmo de firma no está especificado en el contrato — riesgo de implementación débil.
- ⚠️ Sin expiración de timestamp definida — vulnerable a replay attacks si no se implementa correctamente.

## Datos que lee/escribe

- **Lee:** [[entidad-key]] (para obtener el secret)

## Archivos fuente relevantes

- `src/contracts/auth/interfaces/validation.ts` (interfaz `generate-signature`)
- `src/common/cmd/interfaces/auth.ts`

# Funcionalidad: Validar Autorización

**Pattern:** `validate-authorization`  
**Módulo:** Validation  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Verifica si una compañía está autorizada para acceder a un recurso específico (contrato, entidad, etc).

---

## Entrada

```typescript
{
  company_id: number;
  resource_type: string;   // 'contract', 'entity', etc
  resource_id?: number;    // ID del recurso (si aplica)
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    authorized: boolean;
  },
  error: null
}
```

---

## Validaciones

- ✅ Company existe
- ✅ resource_type es válido
- ✅ resource existe (si provided)

---

## Ejemplo 1: Autorizar acceso a contrato

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "validate-authorization",
    "payload": {
      "company_id": 5,
      "resource_type": "contract",
      "resource_id": 42
    }
  }'
```

**Respuesta (Autorizado):**
```json
{
  "success": true,
  "data": {
    "authorized": true
  },
  "error": null
}
```

**Respuesta (No autorizado):**
```json
{
  "success": true,
  "data": {
    "authorized": false
  },
  "error": null
}
```

---

## Ejemplo 2: Autorizar acceso a entidad

```bash
curl -X POST http://localhost:4005 \
  -d '{
    "pattern": "validate-authorization",
    "payload": {
      "company_id": 5,
      "resource_type": "entity",
      "resource_id": 10
    }
  }'
```

---

## Lógica de Autorización

La autorización depende del `resource_type`:

| resource_type | Lógica |
|---|---|
| contract | company_id debe ser dueño del contrato |
| entity | company_id debe poder ver la entidad |
| (otro) | Deny por defecto |

---

## Documentos Relacionados

- [[validation-validate-key|Validar API Key]] - Autenticación
- [[_indice-funcionalidades|Índice]] - Todas las acciones


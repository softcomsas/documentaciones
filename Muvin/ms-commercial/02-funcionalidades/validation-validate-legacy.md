# Funcionalidad: Validar Legacy

**Pattern:** `validate-legacy`  
**Módulo:** Validation  
**Tipo:** READ  
**Última actualización:** 2026-04-27

## Descripción

Validación con el sistema anterior (legacy). Este endpoint existe para compatibilidad hacia atrás. Será deprecado en futuro.

---

## Entrada

```typescript
{
  company_id: number;
  token: string;          // Token del sistema anterior
}
```

---

## Salida

```typescript
{
  success: true,
  data: {
    valid: boolean;
  },
  error: null
}
```

---

## ⚠️ DEPRECACIÓN

> [!warning]
> Este endpoint existe solo por compatibilidad con el sistema anterior.
> Migrar a [[validation-validate-key|validate-key]] lo antes posible.
> Se planea remover en versión 3.0+

---

## Documentos Relacionados

- [[validation-validate-key|Validar API Key]] - RECOMENDADO, usar esto
- [[_indice-funcionalidades|Índice]] - Todas las acciones


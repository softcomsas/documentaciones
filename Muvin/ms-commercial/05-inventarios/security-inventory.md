# Inventario de Seguridad

**Última actualización:** 2026-04-27

## API Keys

| Ubicación | Tipo | Propósito |
|-----------|------|----------|
| `src/modules/validation/` | Generación y validación | Autenticación RPC |
| `keys` table | Almacenamiento | BD MySQL |

---

## Validaciones de Entrada

- ✅ CUIT format validation (11 dígitos)
- ✅ Límites y ranges (limit > 0, balance >= 0)
- ✅ Fecha validations (start < end)
- ✅ Enum validations (status, priority)

---

## Validaciones de Seguridad

- ✅ API key HMAC SHA256
- ✅ Autorización por empresa
- ✅ Validación de existencia de entidades

---

## Información Sensible

| Dato | Ubicación | Protección |
|------|-----------|-----------|
| API Secret | keys table | ❌ NO encriptado (RIESGO) |
| API Key | keys table | ✅ UNIQUE index |
| CUIT | entities table | ✅ UNIQUE, PII |
| Razón Social | entities table | ✅ UNIQUE, PII |

---

## Riesgos Identificados

🔴 **CRÍTICO:**
- API Secret no encriptado en BD

🟡 **ALTO:**
- No hay rate limiting en endpoints
- No hay logging de intentos fallidos

🟢 **BAJO:**
- Sin TLS en TCP (depende de red)

---

## Recomendaciones

1. Encriptar API secret en BD
2. Implementar rate limiting
3. Logging de auditoría completo
4. Usar TLS para comunicación

---

## Documentos Relacionados

- [[../02-funcionalidades/validation-create-key|Create API Key]] - Generación
- [[../02-funcionalidades/validation-validate-key|Validate Key]] - Uso
- [[../01-modulos/modulo-validation|Módulo Validation]] - Implementación


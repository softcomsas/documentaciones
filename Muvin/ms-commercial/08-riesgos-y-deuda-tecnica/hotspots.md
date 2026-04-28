# Hotspots Críticos

**Última actualización:** 2026-04-27

## 🔴 CRÍTICO

### 1. API Secret no Encriptado en BD

**Ubicación:** `keys` table → `secret` column

**Riesgo:** Si BD es comprometida, secrets quedan expuestos.

**Recomendación:**
- Encriptar secrets en BD (AES-256)
- Usar bóveda de secretos (Vault, HashiCorp)
- Rotación periódica de secrets

**Esfuerzo:** 2-3 días

---

### 2. Sin Rate Limiting

**Ubicación:** RPC endpoints

**Riesgo:** Ataques de fuerza bruta en `validate-key`, DoS en create-key.

**Recomendación:**
- Implementar rate limiting por IP/company
- Usar middleware NestJS (throttle)
- Logging de intentos fallidos

**Esfuerzo:** 1 día

---

### 3. Auto-cierre IRREVERSIBLE

**Ubicación:** `contracts.change.balance` cuando balance=0

**Riesgo:** Contrato cerrado no puede reabrirse. Si es error, datos perdidos.

**Recomendación:**
- Confirmación explícita antes de auto-cerrar
- Auditoría con timestamp
- Permitir cambio de status manualmente (solo admin)

**Esfuerzo:** 1 día

---

## 🟡 ALTO

### 4. Sin Validación de Autorización

**Ubicación:** Algunos endpoints no validan que empresa es dueña del contrato

**Riesgo:** Una empresa podría acceder a contratos de otra.

**Recomendación:**
- Validar company_id en TODOS los endpoints
- Usar guards de autorización en NestJS
- Tests de seguridad

**Esfuerzo:** 1-2 días

---

### 5. Logging Insuficiente

**Ubicación:** Sin logs de intentos de acceso fallidos

**Riesgo:** No hay auditoría de ataques o errores.

**Recomendación:**
- Logger centralizado para todas las acciones
- Enviar a ELK stack o equivalente
- Retención mínima 90 días

**Esfuerzo:** 2-3 días

---

### 6. Sin TLS en TCP

**Ubicación:** Transport TCP:4005

**Riesgo:** Datos en tránsito sin encripción (dentro de red privada).

**Recomendación:**
- Implementar TLS en NestJS transport
- Usar certificados self-signed en dev
- Certificados válidos en prod

**Esfuerzo:** 1 día

---

## 🟢 MEDIO

### 7. CUIT Resolved Dinámicamente

**Ubicación:** ContractsService.createContract → findByCuit

**Riesgo:** Si tabla `entities` está fuera de sync, contratos fallan.

**Recomendación:**
- Caché de entidades con TTL
- Monitoreo de sincronización
- Fallback a ms-auth

**Esfuerzo:** 1 día

---

### 8. Sin Caché de Búsquedas

**Ubicación:** search.list, search.all hacen queries directas

**Riesgo:** Operaciones lentas en BD con millones de contratos.

**Recomendación:**
- Redis para caché de búsquedas
- Invalidación en cambios
- TTL de 5-10 minutos

**Esfuerzo:** 2-3 días

---

## Matriz de Riesgos

| Riesgo | Probabilidad | Impacto | Severidad |
|--------|-------------|--------|-----------|
| Secret expuesto | Media | Crítico | 🔴 |
| Fuerza bruta | Alta | Alto | 🔴 |
| Auto-cierre error | Baja | Crítico | 🔴 |
| Autorización bypass | Media | Crítico | 🟡 |
| Auditoría incompleta | Alta | Medio | 🟡 |
| Rendimiento | Media | Medio | 🟢 |

---

## Plan de Mitigación (Priority)

1. **Week 1:** Encriptar secrets, rate limiting
2. **Week 2:** Validación de autorización
3. **Week 3:** Logging centralizado
4. **Month 2:** TLS, caché
5. **Month 3:** Auditoría externa

---

## Documentos Relacionados

- [[deuda-tecnica|Deuda Técnica]] - Problemas de código
- [[recomendaciones-modernizacion|Recomendaciones]] - Plan futuro
- [[../05-inventarios/security-inventory|Inventario de Seguridad]] - Auditoría


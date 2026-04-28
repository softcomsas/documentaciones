# Índice de Funcionalidades RPC

**Última actualización:** 2026-04-27

## Resumen

El microservicio **ms-commercial** expone **12 acciones RPC** distribuidas en dos módulos:
- **7 acciones** en el módulo Contracts (gestión de contratos)
- **5 acciones** en el módulo Validation (autenticación y validación)

---

## 🔵 Módulo Contracts (7 Acciones)

### Creación

| # | Pattern | Descripción | Entrada Principal | Salida Principal |
|---|---------|-----------|-------------------|------------------|
| 1 | `commercial.contracts.create` | Crear nuevo contrato con validaciones | reference, company_cuit, client_cuit, product_code | id, reference, limit |

**Documento:** [[contracts-create|Crear Contrato]]

---

### Búsqueda

| # | Pattern | Descripción | Entrada Principal | Salida Principal |
|---|---------|-----------|-------------------|------------------|
| 2 | `commercial.contracts.search.one` | Buscar un contrato por reference + company | reference, company_cuit | {id, limit, balance, status} |
| 3 | `commercial.contracts.search.list` | Buscar contratos con paginación | page, limit, filters? | contracts[], pagination |
| 4 | `commercial.contracts.search.all` | Buscar todos los contratos sin paginación | filters? | contracts[] |
| 5 | `commercial.contracts.search.reference` | Buscar contrato completo por reference | reference, company_id | contrato completo |

**Documentos:**
- [[contracts-search-one|Buscar Uno]]
- [[contracts-search-list|Buscar Lista]]
- [[contracts-search-all|Buscar Todo]]
- [[contracts-search-reference|Buscar por Reference]]

---

### Modificación

| # | Pattern | Descripción | Entrada Principal | Salida Principal |
|---|---------|-----------|-------------------|------------------|
| 6 | `commercial.contracts.change.limit` | Actualizar cupo de un contrato | contract_id, new_limit | {id, limit} |
| 7 | `commercial.contracts.change.balance` | Actualizar balance (y auto-cerrar si=0) | contract_id, new_balance | {id, balance, status} |

**Documentos:**
- [[contracts-change-limit|Cambiar Cupo]]
- [[contracts-change-balance|Cambiar Balance]]

---

## 🟢 Módulo Validation (5 Acciones)

### Gestión de API Keys

| # | Pattern | Descripción | Entrada Principal | Salida Principal |
|---|---------|-----------|-------------------|------------------|
| 8 | `create-key` | Generar nueva API key para una empresa | company_id | key, secret, active |

**Documento:** [[validation-create-key|Crear API Key]]

---

### Validación de Seguridad

| # | Pattern | Descripción | Entrada Principal | Salida Principal |
|---|---------|-----------|-------------------|------------------|
| 9 | `validate-key` | Verificar que API key + firma son válidas | key, signature | {valid: boolean} |
| 10 | `validate-authorization` | Verificar que compañía puede acceder a recurso | company_id, resource_type | {authorized: boolean} |
| 11 | `validate-legacy` | Validación con sistema anterior (deprecated) | company_id, token | {valid: boolean} |

**Documentos:**
- [[validation-validate-key|Validar API Key]]
- [[validation-validate-authorization|Validar Autorización]]
- [[validation-validate-legacy|Validar Legacy]]

---

### Criptografía

| # | Pattern | Descripción | Entrada Principal | Salida Principal |
|---|---------|-----------|-------------------|------------------|
| 12 | `generate-signature` | Generar HMAC SHA256 para firma de mensaje | key, secret, data | signature |

**Documento:** [[validation-generate-signature|Generar Firma]]

---

## 📊 Matriz de Acciones por Módulo

```
┌─────────────────────────────────┬──────┬──────┐
│ Categoría                        │ Qty  │ %    │
├─────────────────────────────────┼──────┼──────┤
│ Creación                         │ 1    │ 8%   │
│ Búsqueda / Lectura               │ 4    │ 33%  │
│ Modificación                     │ 2    │ 17%  │
│ Gestión de Keys                  │ 1    │ 8%   │
│ Validación / Seguridad           │ 3    │ 25%  │
│ Criptografía                     │ 1    │ 8%   │
├─────────────────────────────────┼──────┼──────┤
│ TOTAL                            │ 12   │ 100% │
└─────────────────────────────────┴──────┴──────┘
```

---

## 🔐 Niveles de Seguridad

### Sin Validación Explícita
Estos endpoints asumen que el cliente ya fue validado en niveles superiores:
- [[contracts-search-one|search.one]]
- [[contracts-search-list|search.list]]
- [[contracts-search-all|search.all]]

### Con Validación de API Key
Estos endpoints REQUIEREN que el cliente se identifique:
- [[validation-validate-key|validate-key]]
- [[validation-validate-authorization|validate-authorization]]

### Críticos de Seguridad
Estos endpoints deben ser protegidos adicionalemente:
- [[contracts-create|contracts.create]] ⚠️ CRÍTICO
- [[contracts-change-limit|contracts.change.limit]] ⚠️ CRÍTICO
- [[contracts-change-balance|contracts.change.balance]] ⚠️ CRÍTICO

---

## 📈 Patrones de Uso

### Patrón 1: Crear Contrato (Flujo Completo)

```
1. Cliente obtiene API key
   POST /admin → create-key
   
2. Cliente obtiene contrato existente
   POST /rpc → commercial.contracts.search.one
   
3. Cliente crea nuevo contrato
   POST /rpc → commercial.contracts.create
   (incluye firma HMAC)
   
4. Sistema retorna ID del nuevo contrato
```

### Patrón 2: Validar Access (Flujo Completo)

```
1. Cliente tiene API key + secret
2. Genera firma HMAC
   POST /rpc → generate-signature
   
3. Valida su propia key
   POST /rpc → validate-key (con firma)
   
4. Si válida, puede usar otros endpoints
```

### Patrón 3: Buscar Contratos (Flujo Completo)

```
1. Cliente busca contratos por lista
   POST /rpc → commercial.contracts.search.list
   
2. Sistema retorna lista paginada
3. Cliente accede a uno específico
   POST /rpc → commercial.contracts.search.reference
```

---

## 🔄 Transiciones de Estado

### Estado de Contrato

```
OPEN
  ├─ change.limit → OPEN (con nuevo limit)
  ├─ change.balance (balance > 0) → OPEN
  ├─ change.balance (balance = 0) → CLOSED (auto)
  └─ (manual) → CLOSED, EXPIRED, VOIDED

CLOSED
  └─ (no transiciones)

EXPIRED
  └─ (no transiciones)

VOIDED
  └─ (no transiciones)
```

---

## 📋 Checklist de Implementación

Para implementar una nueva funcionalidad:

- [ ] Decidir si es en Contracts o Validation
- [ ] Definir Pattern string (`commercial.contracts.xxx` o similar)
- [ ] Definir entrada (DTO/Payload)
- [ ] Definir salida (Response)
- [ ] Implementar en Service (lógica)
- [ ] Implementar en Controller (handler)
- [ ] Agregar validaciones necesarias
- [ ] Escribir tests unitarios (80%+ coverage)
- [ ] Actualizar esta documentación
- [ ] Testear localmente en desarrollo
- [ ] Hacer PR y code review

---

## 📚 Cómo Navegar Esta Sección

### Si necesitas...
- **Crear un contrato** → Lee [[contracts-create|Crear Contrato]]
- **Buscar contratos** → Lee [[contracts-search-list|Buscar Lista]]
- **Validar una API key** → Lee [[validation-validate-key|Validar API Key]]
- **Entender error de validación** → Consulta el documento específico, sección "Errores Comunes"
- **Entender flujos** → Ir a [[../06-flujos-transversales/_indice-flujos|Flujos Transversales]]

---

## 🔗 Enlaces a Documentación Específica

### Contracts (7 docs)
1. [[contracts-create|contracts-create.md]]
2. [[contracts-search-one|contracts-search-one.md]]
3. [[contracts-search-list|contracts-search-list.md]]
4. [[contracts-search-all|contracts-search-all.md]]
5. [[contracts-search-reference|contracts-search-reference.md]]
6. [[contracts-change-limit|contracts-change-limit.md]]
7. [[contracts-change-balance|contracts-change-balance.md]]

### Validation (5 docs)
8. [[validation-create-key|validation-create-key.md]]
9. [[validation-validate-key|validation-validate-key.md]]
10. [[validation-validate-authorization|validation-validate-authorization.md]]
11. [[validation-validate-legacy|validation-validate-legacy.md]]
12. [[validation-generate-signature|validation-generate-signature.md]]

---

## 📞 Estadísticas Rápidas

| Métrica | Valor |
|---------|-------|
| Total de acciones RPC | 12 |
| Acciones de lectura | 4 |
| Acciones de escritura | 5 |
| Acciones de validación | 3 |
| Módulo más activo | Contracts (7) |
| Patrón de nombre más común | `commercial.contracts.*` |
| Respuesta esperada | JSON RPC (success: boolean, data?, error?) |

---

## ⚠️ Notas Importantes

> [!warning]
> **Orden de Operaciones**: Para crear un contrato, primero asegúrate de que la API key es válida. Usa [[validation-validate-key|validate-key]].

> [!info]
> **Paginación**: Todas las búsquedas listables usan paginación basada en page + limit. Ver [[contracts-search-list|search.list]].

> [!danger]
> **Auto-cierre**: Cuando balance llega a 0 en [[contracts-change-balance|change.balance]], el contrato se cierra automáticamente. Esto es IRREVERSIBLE.

> [!info]
> **Validación de Autorización**: Algunos endpoints pueden requerir validación de que la empresa tiene permiso. Revisar [[validation-validate-authorization|validate-authorization]].


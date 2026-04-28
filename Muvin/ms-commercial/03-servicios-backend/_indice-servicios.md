# Índice de Servicios Backend

**Última actualización:** 2026-04-27

## Resumen

Los servicios backend del ms-commercial están documentados a través de sus **RPC MessagePatterns**. Cada patrón corresponde a un servicio específico.

---

## Servicios por Tipo

### 1. Servicios de Contratos

```
┌────────────────────────────────────────────┐
│ Gestión de Contratos Comerciales           │
├────────────────────────────────────────────┤
│ ContractsService                           │
│ ├─ createContract()                        │
│ ├─ searchOne()                             │
│ ├─ searchList()                            │
│ ├─ searchAll()                             │
│ ├─ searchByReference()                     │
│ ├─ changeLimit()                           │
│ └─ changeBalance()                         │
└────────────────────────────────────────────┘
```

**Ubicación:** `src/modules/contracts/contracts.service.ts`

Responsabilidades:
- Crear contratos con validaciones completas
- Resolver CUITs a IDs de empresa
- Buscar contratos con filtros
- Actualizar cupos y balances
- Auto-cerrar cuando balance = 0

---

### 2. Servicios de Validación

```
┌────────────────────────────────────────────┐
│ Autenticación y Validación                 │
├────────────────────────────────────────────┤
│ ValidationService                          │
│ ├─ createApiKey()                          │
│ ├─ validateKey()                           │
│ ├─ validateAuthorization()                 │
│ ├─ validateLegacy()                        │
│ └─ generateSignature()                     │
└────────────────────────────────────────────┘
```

**Ubicación:** `src/modules/validation/validation.service.ts`

Responsabilidades:
- Generar y gestionar API keys
- Validar firmas HMAC SHA256
- Verificar autorización de acceso
- Compatibilidad con sistema legacy
- Generación de firmas criptográficas

---

## Servicios de Infraestructura

### 3. PrismaService (CoreModule)

```
┌────────────────────────────────────────────┐
│ PrismaService (@Global)                    │
├────────────────────────────────────────────┤
│ ├─ $connect()    → Conectar a MySQL        │
│ ├─ $disconnect() → Desconectar graceful    │
│ └─ $transaction() → Transacciones          │
└────────────────────────────────────────────┘
```

**Ubicación:** `src/core/prisma.service.ts`

Responsabilidades:
- ORM a MySQL
- Tipos automáticos desde schema
- Gestión de conexiones
- Transacciones

---

## Repositorios (Data Access Layer)

### ContractsRepository

```typescript
create(data)
findOne(reference, company)
findByReference(company, reference)
findList(filters, pagination)
findAll(filters)
changeOne(id, limit, balance)
exists(company, reference)
count(filters)
```

### EntitiesRepository

```typescript
findByCuit(cuit)         // CUIT → ID
findManyByCuit(cuits)    // Multiple
count(filters)
```

### ProductsRepository

```typescript
findByCode(code)
```

### KeysRepository

```typescript
create(data)
findByKey(key)
update(id, data)
findByCompany(companyId)
```

---

## Inyección de Dependencias

```
AppModule
├─ ConfigModule
├─ CoreModule (@Global)
│  ├─ PrismaService
│  ├─ ContractsRepository (inyecta PrismaService)
│  ├─ EntitiesRepository (inyecta PrismaService)
│  ├─ ProductsRepository (inyecta PrismaService)
│  └─ KeysRepository (inyecta PrismaService)
│
├─ ContractsModule
│  ├─ ContractsController
│  └─ ContractsService (inyecta repositories)
│
└─ ValidationModule
   ├─ ValidationController
   └─ ValidationService (inyecta repositories)
```

---

## Comunicación Inter-Microservicios

El ms-commercial se comunica con otros microservicios para:

### Hacia ms-auth
- Validar API keys
- Obtener información de empresa

### Hacia ms-integrations
- Notificar creación de contrato (email)

### Hacia ms-logs
- Registrar acciones para auditoría (legacy)

---

## Endpoints RPC (Servicios Expostos)

Ver [[../02-funcionalidades/_indice-funcionalidades|Índice de Funcionalidades]] para detalle de cada RPC:

### Contratos (7)
1. commercial.contracts.create
2. commercial.contracts.search.one
3. commercial.contracts.search.list
4. commercial.contracts.search.all
5. commercial.contracts.search.reference
6. commercial.contracts.change.limit
7. commercial.contracts.change.balance

### Validación (5)
8. create-key
9. validate-key
10. validate-authorization
11. validate-legacy
12. generate-signature

---

## Pattern de Respuesta

Todos los servicios retornan respuestas consistentes:

```typescript
{
  success: boolean;
  data: any | null;
  error: string | null;
}
```

Excepciones en service se convierten automáticamente a error responses.

---

## Documentos Relacionados

- [[commercial-endpoints|Endpoints Comerciales]] - Detalle de API contracts
- [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades RPC]] - Cada acción documentada
- [[../01-modulos/_indice-modulos|Módulos NestJS]] - Estructura de código
- [[../04-modelo-de-datos/_indice-entidades|Modelo de Datos]] - Schema Prisma


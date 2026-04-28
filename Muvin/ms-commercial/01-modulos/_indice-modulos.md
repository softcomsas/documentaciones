# Índice de Módulos NestJS

**Última actualización:** 2026-04-27

## Descripción General

El microservicio ms-commercial tiene 3 módulos principales organizados en la carpeta `src/modules/`:

```
src/modules/
├─ contracts/       # Gestión de contratos comerciales (7 RPC actions)
└─ validation/      # Autenticación y validación (5 RPC actions)

src/core/          # @Global - Servicios compartidos
├─ prisma.service.ts
└─ repositories/
   ├─ contracts.repository.ts
   ├─ entities.repository.ts
   ├─ products.repository.ts
   └─ keys.repository.ts
```

---

## 1. ContractsModule

**Ubicación:** `src/modules/contracts/`

**Propósito:** Gestionar el ciclo de vida completo de contratos comerciales.

### Componentes

```typescript
// contracts.module.ts
@Module({
  controllers: [ContractsController],
  providers: [ContractsService],
})
export class ContractsModule {}
```

### ContractsController
7 endpoints RPC que manejan todas las operaciones sobre contratos:

| Action | Pattern | Entrada | Salida |
|--------|---------|---------|--------|
| **Crear** | `commercial.contracts.create` | {reference, company_cuit, client_cuit, ...} | {id, reference, limit} |
| **Buscar 1** | `commercial.contracts.search.one` | {reference, company_cuit} | {id, limit, balance, status} |
| **Buscar Lista** | `commercial.contracts.search.list` | {filters?, page, limit} | {contracts[], pagination} |
| **Buscar Todo** | `commercial.contracts.search.all` | {filters?} | {contracts[]} |
| **Buscar Ref** | `commercial.contracts.search.reference` | {reference, company_id} | {contract completo} |
| **Cambiar Cupo** | `commercial.contracts.change.limit` | {contract_id, new_limit} | {id, limit} |
| **Cambiar Balance** | `commercial.contracts.change.balance` | {contract_id, new_balance} | {id, balance, status} |

### ContractsService
Contiene la lógica de negocio:

```typescript
// Métodos principales
async createContract(payload)        // Validar, resolver CUIT, crear en DB
async searchOne(reference, company)  // Buscar por reference + company
async searchList(filters, page)      // Buscar con paginación, ordenar por end/priority
async searchAll(filters)             // Buscar todos sin paginación
async searchByReference(company, ref)// Buscar contrato completo
async changeLimit(contractId, limit) // Actualizar cupo
async changeBalance(contractId, bal) // Actualizar balance (y auto-cerrar si=0)
```

### Validaciones de Negocio

✅ **Que valida:**
- Reference es numérico
- Company CUIT existe y es resolvible a ID
- Client CUIT existe
- Product code existe
- Balance nunca puede ser negativo
- Cupo debe ser positivo
- Fechas de vigencia son lógicas (start < end)

### Dependencias

- `ContractsRepository` - Acceso a datos de contratos
- `EntitiesRepository` - Resolución CUIT → ID
- `ProductsRepository` - Validar que producto existe
- `ValidationService` - Validar API key (opcional)

---

## 2. ValidationModule

**Ubicación:** `src/modules/validation/`

**Propósito:** Autenticación, validación de credenciales y generación de firmas criptográficas.

### Componentes

```typescript
// validation.module.ts
@Module({
  controllers: [ValidationController],
  providers: [ValidationService],
})
export class ValidationModule {}
```

### ValidationController
5 endpoints RPC para validaciones:

| Action | Pattern | Entrada | Salida |
|--------|---------|---------|--------|
| **Crear Key** | `create-key` | {company_id, ...} | {key, secret, active} |
| **Validar Key** | `validate-key` | {key, signature} | {valid: boolean} |
| **Validar Auth** | `validate-authorization` | {company_id, resource} | {authorized: boolean} |
| **Validar Legacy** | `validate-legacy` | {company_id, token} | {valid: boolean} |
| **Generar Firma** | `generate-signature` | {key, secret, data} | {signature} |

### ValidationService
Contiene la lógica de validación:

```typescript
// Métodos principales
async createApiKey(companyId)         // Generar nueva API key
async validateKey(key, signature)     // Verificar que key es válida y firma correcta
async validateAuthorization(company, resource)  // Verificar autorización
async validateLegacy(companyId, token)// Compatibilidad con sistema anterior
async generateSignature(key, secret, data)  // Generar firma HMAC
```

### Validaciones de Seguridad

✅ **Que valida:**
- La API key existe y está activa
- La firma criptográfica es correcta (HMAC SHA256)
- La compañía está autorizada para el recurso
- El token legacy es válido (si aplica)

### Dependencias

- `KeysRepository` - Acceso a API keys
- `EntitiesRepository` - Validar que empresa existe
- Librerías criptográficas (crypto, hmac)

---

## 3. CoreModule (@Global)

**Ubicación:** `src/core/`

**Propósito:** Proporcionar servicios compartidos globales a toda la aplicación.

### Componentes

```typescript
// core.module.ts
@Global()
@Module({
  providers: [PrismaService, ContractsRepository, EntitiesRepository, ...],
  exports: [PrismaService, ContractsRepository, ...],
})
export class CoreModule {}
```

### PrismaService

Wrapper alrededor de PrismaClient que:
- Se conecta a MySQL en `OnModuleInit`
- Maneja desconexión graceful en `OnModuleDestroy`
- Permite inyección automática en toda la app

```typescript
export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() {
    await this.$connect();
  }
  async onModuleDestroy() {
    await this.$disconnect();
  }
}
```

### Repositories (Data Access Layer)

#### ContractsRepository
```typescript
create(data)
findOne(reference, company)
findByReference(company, reference)
findList(filters, pagination)
findAll(filters, pagination)
changeOne(id, limit, balance)
exists(company, reference)
count(filters)
```

#### EntitiesRepository
```typescript
findByCuit(cuit)         // CUIT → ID
findManyByCuit(cuits)    // Multiple CUITs → IDs
count(filters)
```

#### ProductsRepository
```typescript
findByCode(code)         // Code → ID (para validar producto)
```

#### KeysRepository
```typescript
create(data)
findByKey(key)
update(id, data)
findByCompany(companyId)
```

---

## Árbol de Módulos Completo

```
AppModule
├─ ConfigModule (variables de entorno)
├─ CoreModule (@Global)
│  ├─ PrismaService
│  ├─ ContractsRepository
│  ├─ EntitiesRepository
│  ├─ ProductsRepository
│  └─ KeysRepository
├─ ContractsModule
│  ├─ ContractsController (7 RPC patterns)
│  └─ ContractsService
└─ ValidationModule
   ├─ ValidationController (5 RPC patterns)
   └─ ValidationService
```

---

## Inyección de Dependencias

### Ejemplo: ContractsService recibe dependencias

```typescript
@Injectable()
export class ContractsService {
  constructor(
    private contractsRepo: ContractsRepository,
    private entitiesRepo: EntitiesRepository,
    private productsRepo: ProductsRepository,
    private validationService: ValidationService,  // otro servicio
  ) {}
}
```

NestJS automáticamente resuelve e inyecta las dependencias.

---

## Flujo de Inicialización

```
1. main.ts llama NestFactory.createMicroservice()
2. Lee APP_MODULE
3. Carga ConfigModule
4. Carga CoreModule (@Global)
   └─ OnModuleInit de PrismaService
   └─ await $connect()
5. Carga ContractsModule
   └─ Inyecta repositories en ContractsService
6. Carga ValidationModule
   └─ Inyecta repositories en ValidationService
7. connectMicroservice(Transport.TCP, :4005)
8. listen() → escucha RPC messages
```

---

## Exportaciones e Importaciones

### CoreModule Exporta
- PrismaService
- ContractsRepository
- EntitiesRepository
- ProductsRepository
- KeysRepository

### ContractsModule Exporta
- ContractsService

### ValidationModule Exporta
- ValidationService

Otros módulos pueden inyectar servicios del CoreModule porque es @Global.

---

## Ciclo de Vida de un RPC

```
1. Cliente envía mensaje TCP con pattern "commercial.contracts.create"
2. NestJS Transport recibe el mensaje
3. Busca @MessagePattern('commercial.contracts.create') en controladores
4. Llama a ContractsController.create()
5. Controller delega a ContractsService.createContract()
6. Service usa repositories para acceder a datos
7. Repositories usan PrismaClient para queries a MySQL
8. Respuesta fluye de vuelta: Service → Controller → Transport → Cliente
```

---

## Patrones de Diseño Aplicados

### 1. Module Pattern
- Cada módulo agrupa controller + service
- Encapsulación clara

### 2. Dependency Injection
- Loose coupling entre componentes
- Fácil testing con mocks

### 3. Repository Pattern
- Abstracción de acceso a datos
- Queries en repositorio, no en service

### 4. Service Layer
- Lógica de negocio centralizada
- Validaciones en service, no en controller

### 5. Global Module
- CoreModule compartido por todos
- No hay duplicación de conexiones a BD

---

## Documentos Relacionados

- [[modulo-contracts|Módulo Contracts]] - Detalle de ContractsModule
- [[modulo-validation|Módulo Validation]] - Detalle de ValidationModule
- [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades RPC]] - Cada endpoint documentado
- [[../04-modelo-de-datos/_indice-entidades|Modelo de Datos]] - Entidades y relaciones
- [[../05-inventarios/cross-module-dependencies|Dependencias Entre Módulos]] - Gráfico de acoplamiento

---

## Estadísticas

| Métrica | Valor |
|---------|-------|
| Módulos NestJS | 3 (Core, Contracts, Validation) |
| Controllers | 2 |
| Services | 2 |
| Repositories | 4 |
| RPC Handlers | 12 (7 + 5) |
| Líneas de código aprox. | 2000 - 3000 |


# Módulo Contracts (ContractsModule)

**Ubicación:** `src/modules/contracts/`
**Última actualización:** 2026-04-27

## Propósito

El módulo Contracts gestiona el ciclo de vida completo de contratos comerciales, incluyendo:
- Creación de contratos con validaciones complejas
- Búsqueda por múltiples criterios
- Actualización de cupos y balances
- Auto-cierre automático cuando balance = 0

---

## Estructura de Archivos

```
src/modules/contracts/
├─ contracts.module.ts      # Declaración de módulo
├─ contracts.controller.ts   # 7 RPC handlers
├─ contracts.service.ts      # Lógica de negocio
└─ (no hay DTO separado - payloads tipados inline)
```

---

## ContractsModule

```typescript
import { Module } from '@nestjs/common';
import { ContractsController } from './contracts.controller';
import { ContractsService } from './contracts.service';

@Module({
  controllers: [ContractsController],
  providers: [ContractsService],
  exports: [ContractsService],
})
export class ContractsModule {}
```

**Importado en:** `AppModule`

---

## ContractsController

Ubicación: `src/modules/contracts/contracts.controller.ts`

Expone 7 acciones RPC. Cada una mapea un `@MessagePattern` a un método:

### 1. Create Contract

```typescript
@MessagePattern('commercial.contracts.create')
async create(@Payload() payload: CreateContractDto) {
  return this.contractsService.createContract(payload);
}
```

**Entrada:**
```typescript
{
  reference: number;        // Identificador del contrato en la empresa
  company_cuit: string;     // CUIT de empresa (11 dígitos)
  client_cuit: string;      // CUIT del cliente
  broker_cuit?: string;     // CUIT del corredor (opcional)
  destination_cuit: string; // CUIT del destino
  product_code: number;     // Código del producto
  limit: number;            // Cupo inicial
  volume?: number;          // Volumen (opcional)
  price: Decimal;           // Precio por unidad
  start: Date;              // Fecha inicio
  end: Date;                // Fecha fin
  priority?: string;        // HIGHEST, HIGH, MEDIUM, LOW, LOWEST
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    id: number;
    reference: number;
    limit: number;
    balance: number;
    status: 'OPEN';
  },
  error: null
}
```

---

### 2. Search One

```typescript
@MessagePattern('commercial.contracts.search.one')
async searchOne(@Payload() payload: SearchOneDto) {
  return this.contractsService.searchOne(payload);
}
```

**Entrada:**
```typescript
{
  reference: number;      // Reference del contrato
  company_cuit: string;   // CUIT de empresa
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    id: number;
    reference: number;
    limit: number;
    balance: number;
    status: string;
  },
  error: null
}
```

---

### 3. Search List (Paginado)

```typescript
@MessagePattern('commercial.contracts.search.list')
async searchList(@Payload() payload: SearchListDto) {
  return this.contractsService.searchList(payload);
}
```

**Entrada:**
```typescript
{
  status?: string;        // Filtro opcional
  company_id?: number;    // Filtro opcional
  page: number;           // Página (1-indexed)
  limit: number;          // Resultados por página
  sort?: 'end' | 'priority' | 'reference';
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    contracts: [
      { id, reference, limit, balance, status, priority, end },
      // ...
    ],
    pagination: {
      page: number;
      limit: number;
      total: number;
      pages: number;
    }
  },
  error: null
}
```

---

### 4. Search All (Sin Paginación)

```typescript
@MessagePattern('commercial.contracts.search.all')
async searchAll(@Payload() payload: SearchAllDto) {
  return this.contractsService.searchAll(payload);
}
```

**Entrada:**
```typescript
{
  status?: string;
  company_id?: number;
  // No hay paginación
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    contracts: [
      { id, reference, limit, balance, status, ... },
      // ...
    ]
  },
  error: null
}
```

---

### 5. Search by Reference

```typescript
@MessagePattern('commercial.contracts.search.reference')
async searchByReference(@Payload() payload: SearchReferenceDto) {
  return this.contractsService.searchByReference(payload);
}
```

**Entrada:**
```typescript
{
  reference: number;
  company_id: number;
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    id: number;
    reference: number;
    company: { id, cuit, name };
    client: { id, cuit, name };
    broker: { id, cuit, name };
    destination: { id, cuit, name };
    product: { id, code, name };
    limit: number;
    balance: number;
    volume?: number;
    price: Decimal;
    status: string;
    priority: string;
    start: Date;
    end: Date;
    createdAt: Date;
  },
  error: null
}
```

---

### 6. Change Limit

```typescript
@MessagePattern('commercial.contracts.change.limit')
async changeLimit(@Payload() payload: ChangeLimitDto) {
  return this.contractsService.changeLimit(payload);
}
```

**Entrada:**
```typescript
{
  contract_id: number;
  new_limit: number;  // Nuevo cupo
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    id: number;
    limit: number;
  },
  error: null
}
```

---

### 7. Change Balance

```typescript
@MessagePattern('commercial.contracts.change.balance')
async changeBalance(@Payload() payload: ChangeBalanceDto) {
  return this.contractsService.changeBalance(payload);
}
```

**Entrada:**
```typescript
{
  contract_id: number;
  new_balance: number;  // Nuevo balance
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    id: number;
    balance: number;
    status: string;  // Puede cambiar a CLOSED
  },
  error: null
}
```

> [!danger]
> Si `new_balance` es 0, el contrato se cierra automáticamente (status = CLOSED).

---

## ContractsService

Ubicación: `src/modules/contracts/contracts.service.ts`

Implementa la lógica de negocio. Métodos principales:

### Constructor

```typescript
constructor(
  private contractsRepository: ContractsRepository,
  private entitiesRepository: EntitiesRepository,
  private productsRepository: ProductsRepository,
) {}
```

### createContract()

```typescript
async createContract(payload: CreateContractDto) {
  // 1. Validar entrada
  if (payload.reference < 0) throw new ValidationException('...');
  if (payload.start >= payload.end) throw new ValidationException('...');
  
  // 2. Resolver CUITs a IDs
  const companyId = await this.entitiesRepository.findByCuit(payload.company_cuit);
  if (!companyId) throw new NotFoundException('Company not found');
  
  const clientId = await this.entitiesRepository.findByCuit(payload.client_cuit);
  if (!clientId) throw new NotFoundException('Client not found');
  
  // 3. Validar product existe
  const productId = await this.productsRepository.findByCode(payload.product_code);
  if (!productId) throw new NotFoundException('Product not found');
  
  // 4. Validar unicidad (reference + company)
  const exists = await this.contractsRepository.exists(companyId, payload.reference);
  if (exists) throw new ConflictException('Contract already exists');
  
  // 5. Crear en BD
  const contract = await this.contractsRepository.create({
    reference: payload.reference,
    company: companyId,
    client: clientId,
    broker: brokerId || null,
    destination: destinationId,
    product: productId,
    limit: payload.limit,
    balance: payload.limit,  // Balance inicial = limit
    volume: payload.volume,
    price: payload.price,
    start: payload.start,
    end: payload.end,
    priority: payload.priority || 'MEDIUM',
    status: 'OPEN',
  });
  
  // 6. Retornar respuesta
  return successResponseFn({
    id: contract.id,
    reference: contract.reference,
    limit: contract.limit,
  });
}
```

### searchOne()

```typescript
async searchOne(payload: { reference: number, company_cuit: string }) {
  // Resolver CUIT → ID
  const companyId = await this.entitiesRepository.findByCuit(payload.company_cuit);
  if (!companyId) throw new NotFoundException('Company not found');
  
  // Buscar contrato
  const contract = await this.contractsRepository.findOne(
    payload.reference,
    companyId
  );
  if (!contract) throw new NotFoundException('Contract not found');
  
  return successResponseFn({
    id: contract.id,
    reference: contract.reference,
    limit: contract.limit,
    balance: contract.balance,
    status: contract.status,
  });
}
```

### searchList() - Con Paginación

```typescript
async searchList(payload: SearchListDto) {
  const where = {};
  
  // Aplicar filtros
  if (payload.status) where.status = payload.status;
  if (payload.company_id) where.company = payload.company_id;
  
  // Buscar
  const contracts = await this.contractsRepository.findList(
    where,
    {
      page: payload.page,
      limit: payload.limit,
    }
  );
  
  // Contar total
  const total = await this.contractsRepository.count(where);
  
  return successResponseFn({
    contracts,
    pagination: {
      page: payload.page,
      limit: payload.limit,
      total,
      pages: Math.ceil(total / payload.limit),
    },
  });
}
```

### searchAll() - Sin Paginación

```typescript
async searchAll(payload: SearchAllDto) {
  const where = {};
  if (payload.status) where.status = payload.status;
  if (payload.company_id) where.company = payload.company_id;
  
  const contracts = await this.contractsRepository.findAll(where);
  
  return successResponseFn({ contracts });
}
```

### searchByReference()

```typescript
async searchByReference(payload: { reference: number, company_id: number }) {
  const contract = await this.contractsRepository.findByReference(
    payload.company_id,
    payload.reference
  );
  
  if (!contract) throw new NotFoundException('Contract not found');
  
  return successResponseFn(contract);  // Retorna completo
}
```

### changeLimit()

```typescript
async changeLimit(payload: { contract_id: number, new_limit: number }) {
  if (payload.new_limit <= 0) {
    throw new ValidationException('Limit must be positive');
  }
  
  const contract = await this.contractsRepository.changeOne(
    payload.contract_id,
    payload.new_limit,
    null  // No cambiar balance
  );
  
  return successResponseFn({
    id: contract.id,
    limit: contract.limit,
  });
}
```

### changeBalance()

```typescript
async changeBalance(payload: { contract_id: number, new_balance: number }) {
  if (payload.new_balance < 0) {
    throw new ValidationException('Balance cannot be negative');
  }
  
  // Si nuevo balance es 0, auto-cerrar
  const newStatus = payload.new_balance === 0 ? 'CLOSED' : null;
  
  const contract = await this.contractsRepository.changeOne(
    payload.contract_id,
    null,  // No cambiar limit
    payload.new_balance,
    newStatus
  );
  
  return successResponseFn({
    id: contract.id,
    balance: contract.balance,
    status: contract.status,
  });
}
```

---

## Validaciones de Negocio

### En Creación
- ✅ Reference es numérico y positivo
- ✅ Company CUIT existe (resolvible)
- ✅ Client CUIT existe
- ✅ Broker CUIT existe (si provided)
- ✅ Destination CUIT existe
- ✅ Product code existe
- ✅ (Reference, Company) es única
- ✅ Limit es positivo
- ✅ Start < End
- ✅ Priority es valor válido

### En Búsqueda
- ✅ Paginación es válida (page >= 1, limit >= 1)
- ✅ Status es valor válido si provided

### En Cambios
- ✅ Contract existe
- ✅ New limit es positivo
- ✅ New balance no es negativo
- ✅ Auto-cierre si balance = 0

---

## Dependencias Inyectadas

```typescript
// En service
private contractsRepository: ContractsRepository
private entitiesRepository: EntitiesRepository
private productsRepository: ProductsRepository
private validationService: ValidationService  // (opcional)
```

Todas inyectadas vía constructor.

---

## Manejo de Errores

### Excepciones Lanzadas

| Excepción | Caso | Código HTTP |
|-----------|------|------------|
| ValidationException | Datos inválidos | 400 |
| NotFoundException | Recurso no existe | 404 |
| ConflictException | Recurso ya existe | 409 |
| UnauthorizedException | Falta autorización | 401 |

Todas convertidas a RPC error response:
```typescript
{
  success: false,
  data: null,
  error: "Mensaje de error"
}
```

---

## Tests Unitarios (Recomendados)

```typescript
describe('ContractsService', () => {
  let service: ContractsService;
  let contractsRepo: ContractsRepository;

  beforeEach(async () => {
    const mockRepo = {
      create: jest.fn(),
      findOne: jest.fn(),
      // ...
    };
    
    service = new ContractsService(mockRepo, ...);
    contractsRepo = mockRepo;
  });

  it('should create contract with valid payload', async () => {
    // arrange
    const payload = { reference: 1, company_cuit: '20123456789', ... };
    contractsRepo.create.mockResolvedValue({ id: 1, ... });
    
    // act
    const result = await service.createContract(payload);
    
    // assert
    expect(result.success).toBe(true);
    expect(contractsRepo.create).toHaveBeenCalled();
  });

  it('should throw if company CUIT not found', async () => {
    // arrange
    const payload = { company_cuit: 'invalid', ... };
    
    // act & assert
    await expect(service.createContract(payload))
      .rejects.toThrow(NotFoundException);
  });
});
```

---

## Performance Notes

> [!info]
> **Índices en BD:** Las búsquedas usan índices en (reference, company), status, company. No hay queries N+1 conocidas.

> [!warning]
> **Paginación:** La búsqueda de lista requiere contar todos los resultados para total. En tablas muy grandes, considerar approximate count.

> [!info]
> **Auto-cierre:** Cuando balance = 0, se ejecuta automáticamente en changeBalance(). Esto es rápido porque es una simple actualización.

---

## Documentos Relacionados

- [[_indice-modulos|Índice de Módulos]] - Visión general
- [[modulo-validation|Módulo Validation]] - Autenticación
- [[../02-funcionalidades/contracts-create|Create Contract Detail]] - Funcionalidad específica
- [[../04-modelo-de-datos/entidad-contracts|Entidad Contracts]] - Schema en Prisma
- [[../06-flujos-transversales/flujo-creacion-contrato|Flujo de Creación]] - End-to-end


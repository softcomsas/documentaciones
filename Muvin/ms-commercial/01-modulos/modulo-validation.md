# Módulo Validation (ValidationModule)

**Ubicación:** `src/modules/validation/`
**Última actualización:** 2026-04-27

## Propósito

El módulo Validation proporciona seguridad y autenticación para el microservicio:
- Crear y administrar API keys
- Validar que un cliente tiene credencial válida
- Validar autorización (que una compañía puede acceder a un recurso)
- Generar y verificar firmas criptográficas
- Compatibilidad con sistema legacy anterior

---

## Estructura de Archivos

```
src/modules/validation/
├─ validation.module.ts         # Declaración de módulo
├─ validation.controller.ts      # 5 RPC handlers
├─ validation.service.ts         # Lógica de validación
└─ (no hay DTO separado - payloads tipados inline)
```

---

## ValidationModule

```typescript
import { Module } from '@nestjs/common';
import { ValidationController } from './validation.controller.ts';
import { ValidationService } from './validation.service';

@Module({
  controllers: [ValidationController],
  providers: [ValidationService],
  exports: [ValidationService],
})
export class ValidationModule {}
```

**Importado en:** `AppModule`

---

## ValidationController

Ubicación: `src/modules/validation/validation.controller.ts`

Expone 5 acciones RPC. Cada una mapea un `@MessagePattern` a un método:

### 1. Create API Key

```typescript
@MessagePattern('create-key')
async createKey(@Payload() payload: CreateKeyDto) {
  return this.validationService.createApiKey(payload);
}
```

**Entrada:**
```typescript
{
  company_id: number;        // ID de la compañía
  // El key y secret se generan automáticamente
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    key: string;             // 64 caracteres hexadecimales
    secret: string;          // 128 caracteres hexadecimales
    active: boolean;         // true
    company_id: number;
  },
  error: null
}
```

> [!warning]
> El `secret` solo se retorna una vez. Guardar inmediatamente en cliente.

---

### 2. Validate API Key

```typescript
@MessagePattern('validate-key')
async validateKey(@Payload() payload: ValidateKeyDto) {
  return this.validationService.validateKey(payload);
}
```

**Entrada:**
```typescript
{
  key: string;               // 64 chars
  signature: string;         // HMAC SHA256 signature
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    valid: boolean;
    company_id?: number;     // Si válido
    active?: boolean;        // Si válido
  },
  error: null
}
```

> [!info]
> La firma se calcula: `HMAC-SHA256(key + data, secret)` en hex.

---

### 3. Validate Authorization

```typescript
@MessagePattern('validate-authorization')
async validateAuthorization(@Payload() payload: ValidateAuthDto) {
  return this.validationService.validateAuthorization(payload);
}
```

**Entrada:**
```typescript
{
  company_id: number;
  resource_type: string;     // Ej: 'contract', 'entity'
  resource_id?: number;      // Ej: ID del contrato
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    authorized: boolean;
  },
  error: null
}
```

> [!info]
> Valida si `company_id` tiene permiso para acceder a `resource_type`.

---

### 4. Validate Legacy

```typescript
@MessagePattern('validate-legacy')
async validateLegacy(@Payload() payload: ValidateLegacyDto) {
  return this.validationService.validateLegacy(payload);
}
```

**Entrada:**
```typescript
{
  company_id: number;
  token: string;            // Token del sistema anterior
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    valid: boolean;
  },
  error: null
}
```

> [!warning]
> Este endpoint existe por compatibilidad con sistema anterior. Será deprecado en versión futura.

---

### 5. Generate Signature

```typescript
@MessagePattern('generate-signature')
async generateSignature(@Payload() payload: GenerateSignatureDto) {
  return this.validationService.generateSignature(payload);
}
```

**Entrada:**
```typescript
{
  key: string;              // API key
  secret: string;           // API secret
  data: string;             // Datos a firmar
}
```

**Salida:**
```typescript
{
  success: true,
  data: {
    signature: string;      // HMAC SHA256 en hex
  },
  error: null
}
```

> [!info]
> Útil para cliente que necesita generar su propia firma.

---

## ValidationService

Ubicación: `src/modules/validation/validation.service.ts`

Implementa la lógica de validación y seguridad.

### Constructor

```typescript
constructor(
  private keysRepository: KeysRepository,
  private entitiesRepository: EntitiesRepository,
  private logger: LoggerService,
) {}
```

### createApiKey()

```typescript
async createApiKey(payload: { company_id: number }) {
  // 1. Validar que company existe
  const company = await this.entitiesRepository.findById(payload.company_id);
  if (!company) throw new NotFoundException('Company not found');
  
  // 2. Generar key y secret
  const key = this.generateRandomKey(64);      // Hexadecimal
  const secret = this.generateRandomSecret(128); // Hexadecimal
  
  // 3. Guardar en BD
  const apiKey = await this.keysRepository.create({
    key,
    secret,
    company: payload.company_id,
    active: true,
  });
  
  // 4. Retornar (secret solo retornado aquí)
  return successResponseFn({
    key: apiKey.key,
    secret: apiKey.secret,
    active: apiKey.active,
    company_id: apiKey.company,
  });
}

private generateRandomKey(length: number): string {
  return crypto.randomBytes(length / 2).toString('hex');
}

private generateRandomSecret(length: number): string {
  return crypto.randomBytes(length / 2).toString('hex');
}
```

### validateKey()

```typescript
async validateKey(payload: { key: string, signature: string }) {
  // 1. Buscar key en BD
  const apiKey = await this.keysRepository.findByKey(payload.key);
  if (!apiKey) {
    this.logger.warn(`Invalid key attempt: ${payload.key}`);
    return successResponseFn({
      valid: false,
    });
  }
  
  // 2. Verificar que está activa
  if (!apiKey.active) {
    return successResponseFn({
      valid: false,
    });
  }
  
  // 3. Verificar firma
  const expectedSignature = this.generateSignature(payload.key, apiKey.secret, payload.key);
  if (payload.signature !== expectedSignature) {
    this.logger.warn(`Invalid signature for key: ${payload.key}`);
    return successResponseFn({
      valid: false,
    });
  }
  
  // 4. Válida
  return successResponseFn({
    valid: true,
    company_id: apiKey.company,
    active: true,
  });
}
```

### validateAuthorization()

```typescript
async validateAuthorization(payload: {
  company_id: number,
  resource_type: string,
  resource_id?: number
}) {
  // 1. Validar company existe
  const company = await this.entitiesRepository.findById(payload.company_id);
  if (!company) {
    return successResponseFn({ authorized: false });
  }
  
  // 2. Según resource_type, validar acceso
  switch (payload.resource_type) {
    case 'contract':
      // Validar que company_id es dueño del contrato
      const contract = await this.contractsRepository.findById(payload.resource_id);
      return successResponseFn({
        authorized: contract?.company === payload.company_id,
      });
    
    case 'entity':
      // Validar que puede ver entidad
      return successResponseFn({ authorized: true });  // Simplificado
    
    default:
      return successResponseFn({ authorized: false });
  }
}
```

### validateLegacy()

```typescript
async validateLegacy(payload: { company_id: number, token: string }) {
  // Verificar contra tabla o servicio legacy
  // Implementación depende del sistema anterior
  
  const isValid = await this.checkLegacyToken(
    payload.company_id,
    payload.token
  );
  
  return successResponseFn({
    valid: isValid,
  });
}
```

### generateSignature()

```typescript
async generateSignature(payload: {
  key: string,
  secret: string,
  data: string
}) {
  // Generar HMAC SHA256
  const signature = this.generateHmac(
    payload.key + payload.data,
    payload.secret
  );
  
  return successResponseFn({
    signature,
  });
}

private generateHmac(data: string, secret: string): string {
  return crypto
    .createHmac('sha256', secret)
    .update(data)
    .digest('hex');
}
```

---

## Modelo de Seguridad

### API Key + Secret Model

```
┌─────────────────────────────────────────┐
│ Cliente                                 │
├─────────────────────────────────────────┤
│ Tiene:                                  │
│ ├─ API Key (público, 64 chars)          │
│ └─ API Secret (privado, 128 chars)      │
└────────┬────────────────────────────────┘
         │
         │ Envía mensaje RPC + firma
         │
┌────────▼────────────────────────────────┐
│ ms-commercial                           │
├─────────────────────────────────────────┤
│ 1. Recibe key
│ 2. Busca en BD la entrada con esa key
│ 3. Recupera secret asociado
│ 4. Regenera firma esperada
│ 5. Compara con firma recibida
│ 6. Si coincide → VÁLIDO
└─────────────────────────────────────────┘
```

### Firma HMAC SHA256

```
Datos para firmar:
  key + data_del_mensaje

Clave de firma:
  secret

Algoritmo:
  HMAC-SHA256(datos, secret) → hex string (64 chars)

Ejemplo:
  key = "abc123def456"
  secret = "xyz789uvw012"
  data = "create-contract"
  
  signature = HMAC-SHA256("abc123def456create-contract", "xyz789uvw012")
            = "5f7c3a2e8b4d6c1f9a0e5b3d7c2f8a1e..."
```

---

## Validaciones de Seguridad

### En Creación de Key
- ✅ Company existe
- ✅ Solo una key por company (UNIQUE)
- ✅ Key se genera aleatoriamente (64 chars)
- ✅ Secret se genera aleatoriamente (128 chars)

### En Validación de Key
- ✅ Key existe en BD
- ✅ Key está activa
- ✅ Firma es correcta (HMAC SHA256)
- ✅ Logging de intentos inválidos

### En Validación de Autorización
- ✅ Company existe
- ✅ Según resource_type, validar acceso apropiado
- ✅ Resource existe (si resource_id provided)

---

## Dependencias Inyectadas

```typescript
// En service
private keysRepository: KeysRepository
private entitiesRepository: EntitiesRepository
private contractsRepository: ContractsRepository  // (para validar contratos)
private logger: LoggerService
```

---

## Manejo de Errores

### Excepciones Lanzadas

| Excepción | Caso | Código HTTP |
|-----------|------|------------|
| ValidationException | Datos inválidos | 400 |
| NotFoundException | Company no existe | 404 |
| UnauthorizedException | Key/firma inválida | 401 |
| ConflictException | Key ya existe | 409 |

Todos convertidos a RPC error response.

---

## Integración con Otros Módulos

### Usado por ContractsModule

```typescript
// En ContractsController, antes de procesar
@MessagePattern('commercial.contracts.create')
async create(@Payload() payload) {
  // Opcionalmente validar key
  if (payload.api_key) {
    const isValid = await this.validationService.validateKey({
      key: payload.api_key,
      signature: payload.signature,
    });
    if (!isValid.data.valid) {
      throw new UnauthorizedException();
    }
  }
  
  return this.contractsService.createContract(payload);
}
```

---

## Tests Unitarios (Recomendados)

```typescript
describe('ValidationService', () => {
  let service: ValidationService;
  let keysRepo: KeysRepository;

  beforeEach(async () => {
    const mockRepo = {
      create: jest.fn(),
      findByKey: jest.fn(),
      // ...
    };
    
    service = new ValidationService(mockRepo, ...);
    keysRepo = mockRepo;
  });

  it('should create API key for valid company', async () => {
    // arrange
    const payload = { company_id: 1 };
    keysRepo.create.mockResolvedValue({
      key: 'abc123...',
      secret: 'xyz789...',
      active: true,
    });
    
    // act
    const result = await service.createApiKey(payload);
    
    // assert
    expect(result.success).toBe(true);
    expect(result.data.key).toBeDefined();
  });

  it('should validate correct signature', async () => {
    // arrange
    const key = 'abc123...';
    const secret = 'xyz789...';
    const data = 'test-data';
    const signature = generateHmac(key + data, secret);
    
    keysRepo.findByKey.mockResolvedValue({ secret, active: true });
    
    // act
    const result = await service.validateKey({ key, signature });
    
    // assert
    expect(result.data.valid).toBe(true);
  });

  it('should reject invalid signature', async () => {
    // arrange
    keysRepo.findByKey.mockResolvedValue({ secret: 'xyz789...', active: true });
    
    // act
    const result = await service.validateKey({
      key: 'abc123...',
      signature: 'wrong-signature',
    });
    
    // assert
    expect(result.data.valid).toBe(false);
  });
});
```

---

## Performance Notes

> [!info]
> **API Key Lookup:** O(1) lookup en tabla keys usando índice en `key` column.

> [!warning]
> **HMAC Generation:** Operación criptográfica. Para alto volumen (>1000 RPS), considerar caching de resultados.

> [!info]
> **Logging:** Se loguean intentos de validación fallidos para auditoría.

---

## Security Best Practices

> [!danger]
> **NUNCA** loguear el secret. Solo el key y el resultado de validación.

> [!warning]
> **Secret única oportunidad:** Cuando se genera, se retorna una vez al cliente. Después no se puede recuperar. Guardar inmediatamente.

> [!info]
> **Rate Limiting:** En producción, considerar rate limiting en `validate-key` para prevenir fuerza bruta.

> [!info]
> **HTTPS/TLS:** Aunque TCP es seguro, para máxima seguridad, usar TLS en transporte.

---

## Documentos Relacionados

- [[_indice-modulos|Índice de Módulos]] - Visión general
- [[modulo-contracts|Módulo Contracts]] - Gestión de contratos
- [[../02-funcionalidades/validation-validate-key|Validate Key Detail]] - Funcionalidad específica
- [[../05-inventarios/security-inventory|Inventario de Seguridad]] - Auditoría de seguridad
- [[../04-modelo-de-datos/entidad-keys|Entidad Keys]] - Schema en Prisma


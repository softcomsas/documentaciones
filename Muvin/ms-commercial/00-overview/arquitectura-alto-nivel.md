# Arquitectura de Alto Nivel

**Última actualización:** 2026-04-27

## Diagrama de Capas

```
┌──────────────────────────────────────────────────────────────┐
│                     External Clients                          │
│                  (Microservicios, Usuarios)                  │
└────────────────────────┬─────────────────────────────────────┘
                         │
                   TCP Port 4005
                         │
┌────────────────────────▼─────────────────────────────────────┐
│                  NestJS Application                           │
│                                                               │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              1. API Gateway / Transport                 │ │
│  │  ├─ Transport: TCP (NestMicroservices)                  │ │
│  │  └─ Message Handlers: @MessagePattern                   │ │
│  └────────────────────┬────────────────────────────────────┘ │
│                       │                                        │
│  ┌────────────────────▼────────────────────────────────────┐ │
│  │         2. Controllers (RPC Entry Points)               │ │
│  │                                                          │ │
│  │  ├─ ContractsController                                 │ │
│  │  │  └─ 7 MessagePatterns (create, search, change...)   │ │
│  │  │                                                       │ │
│  │  └─ ValidationController                                │ │
│  │     └─ 5 MessagePatterns (validate, create-key...)      │ │
│  └────────────────────┬────────────────────────────────────┘ │
│                       │                                        │
│  ┌────────────────────▼────────────────────────────────────┐ │
│  │    3. Services (Business Logic Layer)                   │ │
│  │                                                          │ │
│  │  ├─ ContractsService                                    │ │
│  │  │  ├─ Validaciones de negocio                          │ │
│  │  │  ├─ Resolución CUIT → Company ID                    │ │
│  │  │  ├─ Auto-cierre si balance=0                         │ │
│  │  │  └─ Orquestación                                     │ │
│  │  │                                                       │ │
│  │  └─ ValidationService                                   │ │
│  │     ├─ Validar API keys                                 │ │
│  │     ├─ Validar autorización                             │ │
│  │     ├─ Generar firmas                                   │ │
│  │     └─ Compatibilidad legacy                            │ │
│  └────────────────────┬────────────────────────────────────┘ │
│                       │                                        │
│  ┌────────────────────▼────────────────────────────────────┐ │
│  │   4. Repositories (Data Access Layer)                   │ │
│  │                                                          │ │
│  │  ├─ ContractsRepository                                 │ │
│  │  ├─ EntitiesRepository                                  │ │
│  │  ├─ ProductsRepository                                  │ │
│  │  └─ KeysRepository                                      │ │
│  │                                                          │ │
│  │  All use: PrismaService (ORM)                           │ │
│  └────────────────────┬────────────────────────────────────┘ │
│                       │                                        │
└───────────────────────┼────────────────────────────────────────┘
                        │
                    Prisma ORM
                        │
┌───────────────────────▼────────────────────────────────────────┐
│              5. MySQL Database                                  │
│                                                                │
│  ├─ contracts (principal)                                     │
│  ├─ entities (empresa, cliente, broker, destino)              │
│  ├─ products                                                  │
│  ├─ roles                                                     │
│  ├─ entity_roles                                              │
│  └─ keys (API keys para validación)                           │
│                                                                │
│  Host: muvinapp-preprod-db.mysql.database.azure.com           │
│  Database: dev_commercial                                     │
└────────────────────────────────────────────────────────────────┘
```

---

## Estructura de Módulos NestJS

```
ms-commercial (src/)
│
├─ config/                    # Configuración global
│  ├─ environments.ts        # Joi validation (HOST, PORT, DATABASE_URL)
│  ├─ transport.ts           # Transport TCP config
│  └─ _index.ts
│
├─ core/                      # @Global - inyectable en todo el app
│  ├─ prisma.service.ts      # Conexión MySQL
│  ├─ repositories/
│  │  ├─ contracts.repository.ts
│  │  ├─ entities.repository.ts
│  │  ├─ products.repository.ts
│  │  └─ keys.repository.ts
│  └─ services/
│     └─ ... (servicios compartidos si hay)
│
├─ modules/                   # Módulos de negocio
│  ├─ contracts/
│  │  ├─ contracts.module.ts
│  │  ├─ contracts.controller.ts    # 7 RPC handlers
│  │  └─ contracts.service.ts       # Lógica de negocio
│  │
│  └─ validation/
│     ├─ validation.module.ts
│     ├─ validation.controller.ts    # 5 RPC handlers
│     └─ validation.service.ts       # Lógica de validación
│
├─ contracts/                 # Interfaces inter-microservicios
│  ├─ auth/
│  │  ├─ contract.ts
│  │  ├─ interfaces/
│  │  └─ schema.ts
│  ├─ commercial/
│  │  ├─ interfaces/
│  │  ├─ schema.ts
│  │  └─ contract.ts
│  ├─ integrations/
│  ├─ logs/
│  └─ types.ts
│
├─ common/                    # Utilidades compartidas
│  ├─ cmd/                    # MessagePatterns constants
│  │  ├─ constant.ts
│  │  ├─ interface.ts
│  │  ├─ interfaces/
│  │  └─ _index.ts
│  ├─ functions/              # Helper functions
│  │  ├─ logger.ts           # Console con colores ANSI
│  │  ├─ api-response.ts      # successResponseFn, errResponseFn
│  │  ├─ identity.ts
│  │  └─ _index.ts
│  └─ interfaces/             # Tipos compartidos
│     ├─ api-response.ts      # TApi<T>, IPagination
│     ├─ option.ts
│     ├─ option-extended.ts
│     ├─ pagination.ts
│     └─ _index.ts
│
├─ app.module.ts             # Root module (importa config, core, modules)
├─ main.ts                   # Bootstrap (microservicio)
└─ ...
```

---

## Flujo de una Solicitud RPC

### Ejemplo: Crear un Contrato

```
1. Cliente externo conecta por TCP:4005
   └─> Envía mensaje RPC con pattern: "commercial.contracts.create"
   
2. NestJS Transport (TCP)
   └─> Recibe el mensaje
   
3. ContractsController
   └─> @MessagePattern('commercial.contracts.create')
   └─> Extrae payload (reference, company_cuit, client_cuit, ...)
   
4. ContractsService.createContract()
   └─> Valida request
   └─> Resuelve CUIT company → company ID (via EntitiesRepository)
   └─> Resuelve CUIT client → client ID
   └─> Valida que el producto existe (ProductsRepository)
   └─> Crea contrato via ContractsRepository
   └─> Retorna { id, reference, limit, ... }
   
5. ContractsController retorna RPC Response
   └─> { success: true, data: {...}, error: null }
   
6. NestJS Transport (TCP)
   └─> Envía respuesta al cliente
   
7. Cliente recibe respuesta
```

---

## Componentes Principales

### 1. ContractsController (`src/modules/contracts/contracts.controller.ts`)

Maneja 7 acciones RPC:

```typescript
@MessagePattern('commercial.contracts.create')
async create(@Payload() payload)

@MessagePattern('commercial.contracts.search.one')
async searchOne(@Payload() payload)

@MessagePattern('commercial.contracts.search.list')
async searchList(@Payload() payload)

@MessagePattern('commercial.contracts.search.all')
async searchAll(@Payload() payload)

@MessagePattern('commercial.contracts.search.reference')
async searchByReference(@Payload() payload)

@MessagePattern('commercial.contracts.change.limit')
async changeLimit(@Payload() payload)

@MessagePattern('commercial.contracts.change.balance')
async changeBalance(@Payload() payload)
```

### 2. ValidationController (`src/modules/validation/validation.controller.ts`)

Maneja 5 acciones RPC:

```typescript
@MessagePattern('create-key')
async createKey(@Payload() payload)

@MessagePattern('validate-key')
async validateKey(@Payload() payload)

@MessagePattern('validate-authorization')
async validateAuthorization(@Payload() payload)

@MessagePattern('validate-legacy')
async validateLegacy(@Payload() payload)

@MessagePattern('generate-signature')
async generateSignature(@Payload() payload)
```

### 3. Repositories

Encapsulan acceso a datos:

```typescript
// ContractsRepository
create(data): Promise<{ id }>
findOne(reference, company): Promise<Contract | null>
findByReference(company, reference): Promise<Contract | null>
findList(where, pagination): Promise<Contract[]>
findAll(where, pagination): Promise<Contract[]>
changeOne(id, limit, balance): Promise<Contract>
exists(company, reference): Promise<boolean>
count(where): Promise<number>
```

### 4. PrismaService (`src/core/prisma.service.ts`)

ORM para MySQL, proporciona tipado completo:

```typescript
this.prisma.contracts.create(...)
this.prisma.contracts.findUnique(...)
this.prisma.contracts.update(...)
this.prisma.entities.findUnique(...)
```

---

## Validaciones de Seguridad

```
┌──────────────────────────────────────────────────────┐
│  Request ingresa con RPC pattern + payload           │
└────────────┬─────────────────────────────────────────┘
             │
    ┌────────▼─────────┐
    │ Validar API Key? │  ← Algunos endpoints requieren validación
    └────────┬─────────┘
             │
    ┌────────▼──────────────┐
    │ Validar Autorización? │  ← Algunos requieren que empresa = owner
    └────────┬──────────────┘
             │
    ┌────────▼────────────────────┐
    │ Validar datos (tipo, rango) │
    └────────┬────────────────────┘
             │
    ┌────────▼──────────────────────┐
    │ Ejecutar lógica de negocio    │
    └────────┬──────────────────────┘
             │
    ┌────────▼──────────────────────┐
    │ Retornar respuesta RPC        │
    └───────────────────────────────┘
```

---

## Integración con Microservicios

### Llamadas SALIENTES

**ms-commercial → ms-auth**
- Validar API key
- Validar que empresa existe
- Obtener info de empresa

**ms-commercial → ms-integrations**
- Notificar creación de contrato (email)

**ms-commercial → ms-logs**
- Registrar acción para auditoría (legacy)

### Llamadas ENTRANTES

**Otros microservicios → ms-commercial**
- Crear contrato: `commercial.contracts.create`
- Buscar contrato: `commercial.contracts.search.*`
- Cambiar cupo: `commercial.contracts.change.limit`
- Validar key: `validate-key`

---

## Manejo de Errores

```
┌──────────────────────────────┐
│ Validación falla             │
└────────────┬─────────────────┘
             │
    ┌────────▼─────────────────────┐
    │ Throw ValidationException    │
    │ (mensaje descriptivo)        │
    └────────┬────────────────────┘
             │
    ┌────────▼──────────────────────┐
    │ Controller captura exception  │
    │ (try-catch implícito en RPC)  │
    └────────┬─────────────────────┘
             │
    ┌────────▼──────────────────────┐
    │ Retorna error RPC:            │
    │ {                             │
    │   success: false,             │
    │   data: null,                 │
    │   error: "Mensaje"            │
    │ }                             │
    └───────────────────────────────┘
```

---

## Patrones de Diseño

### 1. Repository Pattern
- Abstrae acceso a datos
- Facilita testing (mockear repositorio)
- Concentra queries complejas

### 2. Service Layer
- Contiene lógica de negocio
- Valida datos
- Orquesta múltiples repositorios

### 3. Dependency Injection
- NestJS inyecta automáticamente ServiceA en ServiceB
- Loose coupling, fácil testing

### 4. Global Module
- CoreModule está marcado como @Global
- Todos los módulos pueden inyectar PrismaService

### 5. Message Pattern
- RPC basado en strings (patrón similar a RabbitMQ)
- Fácil de entender qué endpoints existen

---

## Configuración en Tiempo de Ejecución

```
┌──────────────────────────────────────────┐
│ main.ts: Bootstrap Microservicio         │
├──────────────────────────────────────────┤
│ 1. Leer variables de entorno             │
│ 2. Validar con Joi (environments.ts)     │
│ 3. Crear NestJS app                      │
│ 4. Conectar Transport TCP                │
│ 5. Escuchar en HOST:PORT                 │
│ 6. OnModuleInit: Conectar Prisma         │
└──────────────────────────────────────────┘
```

---

## Índice de Documentos Relacionados

- [[../01-modulos/_indice-modulos|Módulos NestJS]] - Detalles de cada módulo
- [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades RPC]] - Cada endpoint documentado
- [[../04-modelo-de-datos/diagrama-er-global|Diagrama E-R]] - Entidades y relaciones
- [[../06-flujos-transversales/_indice-flujos|Flujos Transversales]] - Data flow end-to-end
- [[../05-inventarios/cross-module-dependencies|Dependencias Entre Módulos]] - Matriz de acoplamiento

---

## Notas Importantes

> [!warning]
> **Validación de API Key**: No todos los endpoints requieren validación explícita. Algunas acciones asumen que si llegaron a través de TCP es porque ya fueron validadas por el caliente que las envió. Revisar cada endpoint específicamente en [[../02-funcionalidades/_indice-funcionalidades|Funcionalidades]].

> [!info]
> **Resolución CUIT**: El servicio resuelve CUIT (número de empresa) a ID interno. Esta resolución se hace consultando ms-auth o la tabla entities. Ambos están sincronizados.

> [!danger]
> **Auto-cierre**: Cuando balance llega a 0, el contrato se cierra automáticamente. Esto es importante para auditoría y no puede deshacerse.


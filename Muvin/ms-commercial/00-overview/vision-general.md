# Visión General del Microservicio Comercial

**Última actualización:** 2026-04-27

## ¿Qué es ms-commercial?

El **ms-commercial** es un microservicio especializado en la **gestión de contratos comerciales** para la plataforma MUVIN. Su responsabilidad principal es administrar el ciclo de vida completo de contratos entre empresas, incluyendo creación, búsqueda, actualización y validación.

> [!info]
> **ms-commercial** es un microservicio RPC (Remote Procedure Call) que se comunica por TCP en el puerto **4005**, integrado en la arquitectura de microservicios de MUVIN.

---

## 🎯 Propósito Principal

El microservicio resuelve estos problemas de negocio:

1. **Gestión Centralizada de Contratos** - Un único punto de verdad para todos los contratos comerciales
2. **Control de Cupos** - Administra límites de crédito y balance disponible
3. **Búsqueda Eficiente** - Permite filtrar y localizar contratos por múltiples criterios
4. **Validación de Acceso** - Asegura que solo usuarios autenticados accedan a datos
5. **Integración con Ecosistema** - Se comunica con otros microservicios para enriquecimiento de datos

---

## 📊 Conceptos Clave de Negocio

### Contrato Comercial (Contract)
Un acuerdo entre dos partes donde:
- **Empresa** proporciona servicios o productos
- **Cliente** consume los servicios
- **Broker** actúa como intermediario (opcional)
- **Destino** es la ubicación de destino de los productos

Cada contrato tiene:
- **Reference**: Identificador único del contrato + Empresa (UNIQUE)
- **Limit**: Cupo máximo permitido
- **Balance**: Cantidad disponible restante
- **Price**: Precio por unidad
- **Status**: OPEN, CLOSED, EXPIRED, VOIDED
- **Priority**: HIGHEST, HIGH, MEDIUM, LOW, LOWEST

### Validación de Acceso
Dos mecanismos:
1. **API Keys** - Autenticación para llamadas RPC (create-key, validate-key)
2. **Company Authorization** - Validación de que la empresa puede acceder a ciertos contratos

---

## 🔄 Flujo de Uso General

```
Usuario/Microservicio Externo
          ↓
    Envía RPC Message (TCP)
          ↓
    ms-commercial Controller
          ↓
    Aplica validaciones (API key, autorización)
          ↓
    Delega a Service (lógica de negocio)
          ↓
    Service consulta Repository
          ↓
    Repository usa Prisma → MySQL
          ↓
    Respuesta RPC (success/error)
          ↓
    Usuario/Microservicio recibe resultado
```

---

## 🎪 Actores Principales

### Microservicios que Consumen ms-commercial
- **ms-auth** - Valida API keys, comprueba autorización de empresas
- **ms-integrations** - Recibe notificaciones cuando se crean/actualizan contratos
- **ms-logs** - Registra acciones para auditoría (legacy)
- **Otros microservicios** - Cualquiera que necesite datos de contratos

### Datos que Otros Microservicios Nos Proporcionan
- **ms-auth** - Información de empresas, validación de credenciales
- **Sistema Externo** - Información de entidades (empresa, cliente, broker, destino)

---

## 📌 Responsabilidades de ms-commercial

| ✅ Sí |  ❌ No |
|------|--------|
| Crear contratos con validaciones | Gestionar usuarios o permisos |
| Buscar y filtrar contratos | Gestionar roles de usuario |
| Actualizar cupos y balances | Enviar emails (solo notifica a integrations) |
| Validar API keys | Guardar logs (solo notifica a logs) |
| Resolver CUIT a ID de empresa | Autenticar usuarios (delega a ms-auth) |
| Auto-cerrar si balance = 0 | Gestionar autorización de usuarios |
| Dar respuesta RPC inmediata | Mantener sesiones o tokens |

---

## 🌍 Ecosistema MUVIN

```
┌─────────────────────────────────────────────────────────┐
│                     MUVIN Platform                        │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  ┌──────────────┐    ┌──────────────┐    ┌────────────┐ │
│  │  ms-auth     │    │ ms-integr.   │    │  ms-logs   │ │
│  │ (validation) │    │  (email)     │    │ (auditoría)│ │
│  └────────┬─────┘    └──────┬───────┘    └─────┬──────┘ │
│           │                 │                   │        │
│           └─────────────────┼───────────────────┘        │
│                             │                            │
│                     ┌───────▼────────┐                   │
│                     │ ms-commercial  │                   │
│                     │ (este proyecto)│                   │
│                     └───────┬────────┘                   │
│                             │                            │
│                     ┌───────▼────────┐                   │
│                     │  MySQL Database │                   │
│                     │  (dev_commercial)                  │
│                     └────────────────┘                   │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

---

## 🔐 Seguridad y Autenticación

### Validación de Acceso
1. **Cada RPC request** incluye una API key
2. **ms-commercial** valida la API key contra su propia BD (tabla `keys`)
3. Si es válida, procesa la acción
4. Si no es válida, retorna error `UNAUTHORIZED`

### Autorización
- Algunas acciones requieren validar que la empresa puede acceder a ciertos contratos
- Ejemplo: El contrato debe pertenecer a la empresa que hace la solicitud

---

## 📈 Volumen y Escala

| Métrica | Estimado |
|---------|----------|
| Contratos activos esperados | 10K - 100K |
| Búsquedas por día | 50K - 500K |
| Creación de contratos/día | 100 - 1K |
| Cambios de cupo/balance/día | 500 - 5K |
| Peak RPS esperado | 50 - 200 |

> [!warning]
> Desde el inicio (Nov 2025) se hizo una migración única. El proyecto ha estado en operación estable por ~1 año.

---

## ⏱️ Ciclo de Vida de un Contrato

```
1. CREACIÓN
   - Service valida datos
   - Se crea en status OPEN
   - Se asigna cupo inicial
   
2. OPERACIÓN
   - Balance se decrementa con uso
   - Cupo puede ser actualizado
   - Status permanece OPEN
   
3. CIERRE
   - Manual: cambiar status a CLOSED
   - Automático: si balance llega a 0 (auto-cierre)
   - Por expiración: si fecha end ha pasado
   
4. ARCHIVADO
   - Contrato cerrado permanece en BD
   - Se mantiene para auditoría
   - No se puede reabrir
```

---

## 🔧 Interacciones Típicas

### Caso 1: Crear Contrato
```
Cliente → ms-commercial.contracts.create
       → Valida CUIT, obtiene ID empresa
       → Valida producto existe
       → Crea en DB
       → Notifica ms-integrations (email)
       → Registra en ms-logs
       → Retorna {id, reference, limit}
```

### Caso 2: Buscar Contrato
```
Cliente → ms-commercial.contracts.search.one
       → Valida API key
       → Busca por reference + company
       → Retorna {id, limit, balance, status}
```

### Caso 3: Cambiar Cupo
```
Cliente → ms-commercial.contracts.change.limit
       → Valida API key
       → Busca contrato
       → Actualiza limit
       → Retorna {id, limit}
```

---

## 📋 Responsabilidades por Componente

| Componente | Responsabilidad |
|-----------|-----------------|
| **Controller** | Recibir RPC, parsear payload, retornar respuesta |
| **Service** | Validaciones de negocio, resolución CUIT→ID, orquestación |
| **Repository** | Acceso a Prisma, queries a MySQL |
| **Prisma** | ORM, tipado de datos, migraciones |
| **MySQL** | Persistencia, índices, constraints |

---

## 🎓 Convenciones del Proyecto

### Nombres RPC
```
commercial.<modulo>.<accion>
Ejemplo: commercial.contracts.create
```

### Tipos de Respuesta
```typescript
// Éxito
{ success: true, data: {...}, error: null }

// Error
{ success: false, data: null, error: "Mensaje" }
```

### Naming Convention
- Controllers: `*.controller.ts`
- Services: `*.service.ts`
- Repositories: `*.repository.ts`
- Interfaces: `*.interface.ts` o `.ts` en carpeta `interfaces/`

---

## 📚 Documentos Relacionados

- [[arquitectura-alto-nivel|Arquitectura de Alto Nivel]] - Diagrama técnico detallado
- [[stack-tecnologico|Stack Tecnológico]] - Versiones y dependencias
- [[glosario|Glosario]] - Términos de negocio y técnicos
- [[01-modulos/_indice-modulos|Índice de Módulos]] - Descripción de módulos NestJS
- [[06-flujos-transversales/_indice-flujos|Flujos Transversales]] - Data flow end-to-end

---

## 🚀 Próximos Pasos

Si eres nuevo en este proyecto:
1. Lee esta visión general (~10 min) ✅
2. Revisa [[arquitectura-alto-nivel|Arquitectura de Alto Nivel]] (~15 min)
3. Consulta [[stack-tecnologico|Stack Tecnológico]] para setup (~10 min)
4. Mira [[01-modulos/_indice-modulos|Índice de Módulos]] para código (~20 min)
5. Ejecuta `npm install` y `npm run dev` para correr localmente


# Glosario de Términos

**Última actualización:** 2026-04-27

## Términos de Negocio

### Contrato Comercial (Contract)
Acuerdo vinculante entre dos partes donde se define:
- **Empresa proveedora** quien ofrece servicios/productos
- **Cliente** quien consume
- **Cupo límite** monto máximo permitido
- **Balance** cupo disponible restante
- **Período de vigencia** fechas de inicio y fin

Cada contrato tiene un **Reference** (número único por empresa) que lo identifica junto con la empresa.

### Reference (Referencia)
Identificador del contrato único dentro de una empresa. Ejemplo: REF-001, REF-002, etc.
Junto con `company_id`, forma la clave única del contrato.

### Cupo (Limit)
Monto máximo total permitido usar en el contrato. No cambia a menos que se actualice explícitamente.

### Balance (Saldo)
Monto disponible restante para usar en el contrato. Se decrementa con cada uso.
Cuando `balance = 0`, el contrato se cierra automáticamente.

### CUIT
Código Único de Identificación Tributaria (Argentina).
Número de 11 dígitos que identifica uniquely a una empresa.
Formato: XX-XXXXXXXX-X

### Entity (Entidad)
Representación de una empresa, cliente, broker o destino en el sistema.
Cada entidad tiene un CUIT único y razón social.

### Product (Producto)
Bien o servicio que puede ser ofrecido en un contrato.
Identifica por `code` (único) y tiene un `name` (único).

### Priority (Prioridad)
Orden de importancia de un contrato: HIGHEST, HIGH, MEDIUM, LOW, LOWEST.
Usado para ordenar contratos en búsquedas.

### Status (Estado)
Estado actual del contrato:
- **OPEN**: Activo, puede usarse
- **CLOSED**: Cerrado (manualmente o por balance=0)
- **EXPIRED**: Pasó la fecha de vencimiento
- **VOIDED**: Anulado

### Broker (Corredor)
Intermediario entre proveedor y cliente. Opcional en un contrato.

### Destination (Destino)
Ubicación donde se entrega/utiliza el producto/servicio. Identificado por una entidad.

---

## Términos Técnicos

### Microservicio
Pequeño servicio independiente que cumple una función específica.
En MUVIN, ms-commercial gestiona contratos comerciales.

### RPC (Remote Procedure Call)
Mecanismo para llamar funciones en un servidor remoto como si fuera local.
En este proyecto, usa TCP como transporte.

### TCP
Protocolo de comunicación reliable (confiable) entre máquinas.
Puerto: 4005 para ms-commercial.

### Message Pattern
Identificador de string para una acción RPC.
Ejemplo: `commercial.contracts.create`, `validate-key`.

### Payload
Datos enviados en una solicitud RPC.
Contiene parámetros de entrada para la acción.

### Repository
Patrón de diseño que encapsula acceso a datos (base de datos).
Proporciona métodos como `create()`, `findOne()`, `update()`, etc.

### Service
Capa que contiene lógica de negocio.
Usa repositories para acceder a datos.

### Controller
Recibe solicitudes RPC y delega al servicio.
Mapea Message Patterns a métodos.

### Module (Módulo NestJS)
Unidad de organización en NestJS.
Contiene Controllers, Services, Providers.

### Prisma
ORM (Object-Relational Mapping) que abstrae consultas SQL.
Genera cliente TypeScript tipado automáticamente.

### ORM
Abstracción que mapea tablas a objetos de programación.
Permite escribir queries en código sin SQL raw.

### Global Module
Módulo NestJS que es inyectable en toda la aplicación.
En este proyecto: CoreModule proporciona PrismaService a todos.

### Dependency Injection (DI)
Patrón donde dependencias se "inyectan" en lugar de instanciarse manualmente.
NestJS lo hace automáticamente con decoradores.

### Decorador
Sintaxis TypeScript `@NombreDecorador` que añade funcionalidad a una clase/método.
Ejemplo: `@MessagePattern()`, `@Global()`.

### Schema Prisma
Definición declarativa de tablas, relaciones y constraints.
Archivo: `prisma/schema.prisma`.

### Migration
Script que modifica la estructura de la BD (agregar columnas, tablas, etc.).
Ejecutado con `npx prisma migrate deploy`.

### PrismaClient
Cliente generado automáticamente por Prisma.
Proporciona métodos type-safe para queries.

### Transport
Protocolo de comunicación para microservicios.
En este proyecto: TCP (no HTTP, no RabbitMQ).

### API Key
Credencial de acceso para validar que un cliente tiene permiso de usar el microservicio.
Almacenada en tabla `keys`.

### Signature
Firma criptográfica que valida integridad y autenticidad de un mensaje.
Generada usando API key + secret.

### Validación
Proceso de verificar que datos cumplen ciertos criterios (tipo, rango, formato).

### Authorization (Autorización)
Verificación de que un usuario/cliente tiene permiso para acceder a un recurso.

### Authentication (Autenticación)
Verificación de identidad (quién eres).
En este proyecto: validar que tienes una API key válida.

---

## Términos de Operación

### Environment (Entorno)
Conjunto de variables de configuración.
Ejemplos: LOCAL, DEV, STAGING, PROD.

### Variable de Entorno (Environment Variable)
Configuración que se pasa al proceso en tiempo de ejecución.
Ejemplo: `DATABASE_URL`, `COMMERCIAL_MICROSERVICE_PORT`.

### .env
Archivo que contiene variables de entorno en formato key=value.
Cargado automáticamente en desarrollo.

### Docker
Herramienta para containerizar aplicaciones.
Permite empacar app + dependencias en una imagen.

### Container (Contenedor)
Instancia en ejecución de una imagen Docker.
Aislado, reproducible, portable.

### Image (Imagen)
Plantilla para crear contenedores.
Define qué corre y cómo se configura.

### Registry (Registro)
Repositorio central de imágenes Docker.
En este proyecto: `docker-registry.alternativasinteligentes.com`.

### Deployment (Despliegue)
Proceso de llevar código a un entorno de producción.
Incluye build, push a registry, pull en servidor, restart.

### CI/CD
**CI** (Continuous Integration): Integración continua de código.
**CD** (Continuous Deployment): Despliegue automático.
En este proyecto: GitHub Actions.

### Workflow
Secuencia automatizada de pasos. En GitHub Actions: en `.github/workflows/`.

### Webhook
URL que GitHub llama cuando ocurre un evento (push, PR, release).

### SSH
Protocolo seguro para conectarse remotamente a un servidor.
Usado para ejecutar comandos de deploy.

### Port (Puerto)
Número que identifica un servicio en una máquina.
Puerto TCP: 4005 para ms-commercial.

### Host
Dirección IP o nombre de dominio donde escucha el servicio.
Ejemplo: `localhost`, `0.0.0.0`, `docker-registry.alternativasinteligentes.com`.

### Database Connection String (Connection URL)
String que especifica cómo conectarse a una BD.
Formato MySQL: `mysql://user:pass@host:port/database`.

### Azure
Plataforma cloud de Microsoft.
En este proyecto: BD MySQL alojada en Azure.

---

## Términos de Testing

### Unit Test
Test que verifica una unidad de código (función, método) aisladamente.

### Integration Test
Test que verifica múltiples componentes trabajando juntos (controller + service + repository).

### E2E Test (End-to-End)
Test que verifica un flujo completo desde entrada hasta salida.

### Mock
Simulación fake de un objeto/servicio para testing.
Permite aislar el código bajo test.

### Coverage (Cobertura)
Porcentaje de código que se ejecuta en tests.
Meta: 80% mínimo en este proyecto.

### Test Suite
Conjunto de tests agrupados (por módulo, por función, etc.).

---

## Abreviaciones Comunes

| Abrevia | Significado |
|---------|------------|
| API | Application Programming Interface |
| BD / DB | Base de Datos / Database |
| CUIT | Código Único de Identificación Tributaria |
| CI/CD | Continuous Integration / Continuous Deployment |
| CRUD | Create, Read, Update, Delete |
| DTO | Data Transfer Object |
| E2E | End-to-End |
| HTTP | HyperText Transfer Protocol |
| ORM | Object-Relational Mapping |
| RPC | Remote Procedure Call |
| SSH | Secure Shell |
| TCP | Transmission Control Protocol |
| URL | Uniform Resource Locator |

---

## Mapeo de Términos: SQL ↔ Código

| SQL | TypeScript / Prisma | Significado |
|-----|---------------------|------------|
| TABLE | model | Definición de tabla |
| COLUMN | field | Columna de tabla |
| ROW | record/instance | Fila de datos |
| PRIMARY KEY | @id | Clave primaria |
| FOREIGN KEY | @relation | Referencia a otra tabla |
| UNIQUE | @unique | Valor único |
| SELECT * FROM | findMany() | Buscar todos |
| SELECT ... WHERE | findUnique() / findFirst() | Buscar uno |
| INSERT INTO | create() | Crear nuevo |
| UPDATE | update() | Actualizar |
| DELETE | delete() | Eliminar |
| CONSTRAINT | @db.VarChar(n) | Restricción de tipo |
| INDEX | @@index | Índice para búsquedas rápidas |

---

## Contexto Histórico

### Nov 2025: Migración Única
- El proyecto sufrió una migración importante
- Pasó de arquitectura anterior a NestJS 11 + Prisma 6
- Desde entonces, ha estado estable en producción (~1 año a la fecha actual)
- No se esperan migraciones mayores en corto plazo

---

## Documentos Relacionados

- [[vision-general|Visión General]] - Contexto del proyecto
- [[arquitectura-alto-nivel|Arquitectura]] - Diagramas técnicos
- [[stack-tecnologico|Stack Tecnológico]] - Versiones y setup
- [[../01-modulos/_indice-modulos|Módulos]] - Estructura de código
- [[../04-modelo-de-datos/diagrama-er-global|Diagrama E-R]] - Entidades y relaciones


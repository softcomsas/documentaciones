# Glosario — muvin-api

> **Última revisión:** 2026-04-29

---

| Término | Definición |
|---------|-----------|
| **BFF** | *Backend For Frontend* — API diseñada específicamente para servir a un cliente concreto (app, panel) |
| **ClientProxy** | Clase de NestJS (`@nestjs/microservices`) que permite comunicarse con un microservicio vía TCP, Redis, NATS, etc. |
| **code-first** | Estrategia de GraphQL donde el schema `.gql` se genera automáticamente a partir de los decoradores TypeScript (`@ObjectType`, `@Field`, etc.) |
| **emit()** | Método de `ClientProxy` — envía un mensaje sin esperar respuesta (fire-and-forget). Usado para logs |
| **send()** | Método de `ClientProxy` — envía un mensaje y retorna un `Observable` con la respuesta. Usado para queries |
| **firstValueFrom** | Función RxJS que convierte un `Observable` en una `Promise` tomando el primer valor emitido |
| **DTO** | *Data Transfer Object* — clase que define la forma de los datos de entrada/salida, con decoradores de validación |
| **ValidationPipe** | Pipe global de NestJS que valida automáticamente los DTOs usando `class-validator` |
| **AllExceptionsFilter** | Filtro de excepciones global que captura cualquier error y lo normaliza antes de enviarlo al cliente |
| **GraphqlLoggingInterceptor** | Interceptor personalizado que registra las operaciones GraphQL que llegan al servidor |
| **ms-legacy** | Microservicio interno que gestiona datos históricos/legados del sistema (compradores, etc.) |
| **ms-logs** | Microservicio interno que gestiona logs de auditoría de toda la plataforma |
| **descargas-app** | Aplicación interna en `panel.muvinapp.com/descargas` que genera archivos descargables |
| **Temporary** | Nombre del módulo de proxy hacia descargas-app; indica que es una solución provisional |
| **TCP transport** | Protocolo de comunicación entre muvin-api y los microservicios internos |
| **autoSchemaFile** | Opción de `@nestjs/graphql` que genera automáticamente el archivo `schema.gql` |
| **CMDS** | Objeto de constantes que define los nombres de los mensajes/eventos enviados a microservicios |
| **IRequests / TContractMsLogs** | Tipos TypeScript que definen los contratos de comunicación con cada microservicio |
| **Husky** | Herramienta de Git hooks (pre-commit, pre-push) para ejecutar linting antes de commits |

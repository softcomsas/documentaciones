# Flujo: Bootstrap del microservicio

> **Tipo:** `flowchart TD`
> **Archivos involucrados:** `src/main.ts`, `src/config/environments.ts`, `src/module.ts`

## Descripción

Proceso de arranque del microservicio desde la invocación de `node dist/main.js` hasta que el listener TCP está activo y listo para recibir mensajes.

## Diagrama de arranque

```mermaid
flowchart TD
    START(["node dist/main.js"])
    ENV["Carga dotenv\n(.env en desarrollo)"]
    JOI["Joi valida variables de entorno\n(environments.ts)"]
    JOI_FAIL["throw Error\n'Configuration validation error: ...'"]
    TRANSPORT["Valida que TRANSPORT sea\nun enum válido de NestJS"]
    TRANSPORT_FAIL["throw Error\n'Invalid transport value: ...'"]
    NEST["NestFactory.createMicroservice\n(AppModule, { transport: TCP, host, port })"]
    PIPE["Registra ValidationPipe global\n(whitelist, forbidNonWhitelisted)"]
    LISTEN["app.listen()\nTCP en HOST:PORT"]
    LOG["LOG '[MAIN] Legacy Microservice running on port X'"]
    READY(["✅ Microservicio listo para recibir mensajes"])

    START --> ENV
    ENV --> JOI
    JOI -->|"Error de validación"| JOI_FAIL
    JOI_FAIL --> CRASH([Proceso termina])
    JOI -->|"Válido"| TRANSPORT
    TRANSPORT -->|"Transport inválido"| TRANSPORT_FAIL
    TRANSPORT_FAIL --> CRASH
    TRANSPORT -->|"TCP = 0"| NEST
    NEST --> PIPE
    PIPE --> LISTEN
    LISTEN --> LOG
    LOG --> READY
```

## Variables validadas en el arranque

Ver [[modulo-config]] para la lista completa. Si alguna falta o tiene tipo incorrecto, el proceso cae con un error descriptivo **antes** de que el listener TCP se active. Este comportamiento es correcto (fail-fast).

---
tags: [modelo de datos, base de datos, MySQL]
última-revisión: 2026-04-27
---

# Índice de entidades del modelo de datos

> **Base de datos:** MySQL 8.0
> **ORM:** Prisma v6
> **Archivo de esquema:** `prisma/schema.prisma`
> **Client generado en:** `prisma/generated/client/` (alias `@db`)

## Estado actual

> [!warning] Esquema vacío
> El archivo `prisma/schema.prisma` existe y está correctamente configurado (datasource MySQL + generador del cliente), pero **no tiene ningún modelo definido todavía**.
>
> Las entidades serán documentadas aquí a medida que se agreguen al esquema y se ejecuten las migraciones correspondientes.

## Configuración del datasource

```prisma
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
  output   = "./generated/client"
}
```

## Entidades a implementar (inferidas de los contratos)

De los tipos en `src/contracts/` se infieren las siguientes entidades probables para este microservicio:

| Entidad probable | Base | Estado |
|-----------------|------|--------|
| 🚧 Por definir | Los modelos de este MS dependen de su funcionalidad de dominio aún no implementada | Pendiente |

> [!info] Nota sobre entidades de otros microservicios
> `SchemeAuthCompany`, `IContractSearchOneResponse` y similares son tipos de **respuesta** de otros microservicios (MsAuth, MsCommercial). No son entidades locales de este MS. Este MS tendrá sus propias entidades en Prisma según la funcionalidad que implemente.

## Diagrama ER

> [!warning] Pendiente
> Sin modelos en el esquema, no hay diagrama ER. Se generará aquí cuando se definan los primeros modelos Prisma.

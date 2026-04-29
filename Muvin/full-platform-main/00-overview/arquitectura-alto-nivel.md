# Arquitectura de Alto Nivel

> **Última revisión:** 2026-04-29

## Descripción General

La plataforma utiliza una arquitectura de **microfrontends con Module Federation** sobre Angular 16. El `main` actúa como host (shell) que carga bajo demanda los microfrontends remotos. Los backends son independientes por dominio: los módulos nuevos usan NestJS 10, los módulos legacy usan Yii2 PHP.

## Diagrama de Arquitectura

```mermaid
graph TD
    subgraph Cliente["🌐 Capa de Presentación"]
        Browser["Browser"]
        Shell["main — Shell Angular 16\n(Module Federation Host)"]
        MFE_Auth["auth-app\n(MFE Angular)"]
        MFE_Desc["descargas-app\n(MFE Angular)"]
        MFE_Log["logistica-app\n(MFE Angular)"]
        MFE_Ofer["oferta-app\n(MFE Angular)"]
        MFE_Doc["documentacion-app\n(MFE Angular)"]
        MFE_Ped["pedidos-app\n(MFE Angular)"]
        MFE_Super["superapp\n(MFE Angular)"]
        Mobile_Ped["pedidos-mobile\n(Ionic/Angular)"]
        Mobile_Super["superapp-mobile\n(Ionic/Angular)"]
    end

    subgraph Logic["⚙️ Capa de Lógica de Negocio"]
        NestDesc["NestJS — descargas-app\n(Módulos: descargas, reportes,\ncron, transferencia, adenda,\ncartaPorteTren, txt-upload)"]
        NestPed["NestJS — pedidos-app\n(🚧 En desarrollo)"]
        NestSuper["NestJS — superapp\n(🚧 En desarrollo)"]
        Yii2Log["Yii2 PHP — logistica-app\n(Controllers: Viaje, Carga,\nChofer, Equipo, Oferta...)"]
        Yii2Ofer["Yii2 PHP — oferta-app\n(Controllers: Oferta...)"]
        Yii2Doc["Yii2 PHP — documentacion-app\n(Controllers: Documento,\nCategoria, Entidad...)"]
    end

    subgraph Data["🗄️ Capa de Datos"]
        DB[("MySQL / MariaDB\nvía Sequelize (NestJS)\nvía ActiveRecord (Yii2)")]
    end

    subgraph Ext["🔌 Integraciones Externas"]
        AFIP["AFIP\n(Cupos de descarga)"]
        ExtAPI["APIs Externas de Granos\n(Terminales, precios, etc.)"]
    end

    Browser --> Shell
    Shell -->|"lazy load MFE"| MFE_Auth
    Shell -->|"lazy load MFE"| MFE_Desc
    Shell -->|"lazy load MFE"| MFE_Log
    Shell -->|"lazy load MFE"| MFE_Ofer
    Shell -->|"lazy load MFE"| MFE_Doc
    Shell -->|"lazy load MFE"| MFE_Ped
    Shell -->|"lazy load MFE"| MFE_Super

    MFE_Desc -->|HTTP/REST| NestDesc
    MFE_Ped -->|HTTP/REST| NestPed
    MFE_Super -->|HTTP/REST| NestSuper
    Mobile_Ped -->|HTTP/REST| NestPed
    Mobile_Super -->|HTTP/REST| NestSuper
    MFE_Log -->|HTTP/REST| Yii2Log
    MFE_Ofer -->|HTTP/REST| Yii2Ofer
    MFE_Doc -->|HTTP/REST| Yii2Doc

    NestDesc --> DB
    NestPed --> DB
    NestSuper --> DB
    Yii2Log --> DB
    Yii2Ofer --> DB
    Yii2Doc --> DB

    NestDesc -->|API REST| AFIP
    NestDesc -->|API REST| ExtAPI
```

## Descripción de Capas

### 🌐 Presentación
- **Shell (`main`):** aplicación Angular 16 que actúa como host de Module Federation. Gestiona la navegación principal, el layout (Vex theme), la autenticación global y el lazy loading de los MFEs remotos.
- **MFEs remotos:** cada `*-app/frontend` es un microfrontend Angular independiente, compilable y ejecutable de forma autónoma. Se exponen como `remote` de Webpack Module Federation.
- **Mobile:** las apps de `pedidos` y `superapp` tienen versiones móviles con Ionic/Angular.

### ⚙️ Lógica de Negocio
- **NestJS (nuevos):** backends en TypeScript con arquitectura modular (módulos, controladores, servicios). Usan Sequelize ORM para la base de datos y tienen middleware de autenticación por token JWT.
- **Yii2 PHP (legacy):** backends PHP con patrón MVC activo. Gestión de autenticación propia, ActiveRecord para DB, Swagger integrado parcialmente.

### 🗄️ Datos
- Base de datos relacional (MySQL/MariaDB inferido por Sequelize + Yii2 ActiveRecord).
- El acceso desde NestJS es vía `@nestjs/sequelize`. Desde Yii2 es vía ActiveRecord.

### 🔌 Integraciones Externas
- **AFIP:** actualización de cupos de descarga en `descargas-app/backend`.
- **APIs externas:** servicio `external.api.service.ts` en descargas-app para consulta de datos de terminales y granos.

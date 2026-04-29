# Glosario

> **Última revisión:** 2026-04-29

## Términos de Negocio

| Término | Definición |
|---|---|
| **Cupo** | Autorización de volumen de grano que puede recibirse en una terminal en un período determinado. Se gestiona y actualiza contra AFIP. |
| **Descarga** | Proceso de recepción física de grano en una terminal o planta. Está asociada a un cupo, un camión, un chofer y una carta porte. |
| **Carta Porte** | Documento oficial que avala el transporte de granos en Argentina. Existen dos tipos: camión y tren. Integrado con AFIP. |
| **Adenda** | Documento adicional o modificatorio asociado a una descarga o carta porte. |
| **CTG** | Código de Trazabilidad de Granos. Código oficial generado por AFIP asociado a cada carta porte de granos. |
| **Cupo AFIP** | Cupo de descarga oficialmente registrado y validado en el sistema de AFIP. |
| **Transferencia** | Proceso de transferencia de cupo o grano entre terminales o empresas. |
| **Viaje** | Registro de un trayecto logístico con carga, chofer, equipo y destino. |
| **Carga** | Unidad de mercadería transportada en un viaje. Puede tener cargas derivadas. |
| **Derivación** | Redireccionamiento de una carga hacia un destino diferente al original. |
| **Intermediario** | Empresa o persona que actúa como intermediario comercial en una operación de granos. |
| **Oferta** | Propuesta comercial de compra/venta de granos con precio, volumen y condiciones. |
| **Tablero de Ofertas** | Vista de resumen de las ofertas activas del mercado. |
| **Entidad** | En el contexto de documentación: empresa o persona jurídica que puede cargar documentos en el sistema. |
| **Presentación** | Carga de un documento por parte de una entidad para su revisión o archivo. |
| **Vencimiento** | Fecha límite de un documento presentado. El sistema genera alertas de vencimientos próximos. |
| **Terminal** | Planta física de acopio, procesamiento o carga de granos. |
| **Terminal de Granos** | Ver *Terminal*. En código: `terminales-granos.model.ts`. |

## Términos Técnicos

| Término | Definición |
|---|---|
| **MFE** | Microfrontend. Aplicación Angular independiente que se carga dinámicamente en el shell vía Module Federation. |
| **Shell** | Aplicación host (`main`) que carga y enruta los microfrontends remotos. |
| **Module Federation** | Técnica de Webpack 5 que permite compartir módulos entre aplicaciones en runtime. |
| **Remote Entry** | Punto de entrada de un MFE remoto. Cada `*-app/frontend` expone un `remote-entry` para ser cargado por el shell. |
| **NGXS** | Librería de State Management para Angular basada en el patrón Redux. |
| **Nx** | Herramienta de build y monorepo que gestiona las dependencias y tareas de compilación del workspace. |
| **NestJS** | Framework backend TypeScript orientado a módulos, inspirado en Angular. |
| **Yii2** | Framework PHP MVC legacy usado en los backends de logística, oferta y documentación. |
| **ActiveRecord** | Patrón ORM usado en Yii2 donde cada modelo representa una tabla de base de datos. |
| **Sequelize** | ORM para Node.js/TypeScript usado en los backends NestJS. |
| **DTO** | Data Transfer Object. Clase usada para tipar y validar los datos de entrada a los endpoints. |
| **Interceptor** | Clase NestJS que intercede en el ciclo de request/response. En descargas: `LoggingInterceptor`. |
| **Middleware** | En NestJS: función que se ejecuta antes del handler. En descargas: `TokenMiddleware` valida JWT. |
| **Guard** | En Angular: clase que protege rutas evaluando condiciones antes de la navegación. |
| **Vex** | Tema de UI Angular embebido en `@vex/` dentro del shell y algunos MFEs. |
| **Cron** | Tarea programada que se ejecuta automáticamente en intervalos definidos. En descargas: `CronModule`. |
| **proxy.conf.json** | Archivo de configuración del proxy de desarrollo Angular que redirige llamadas `/api/*` al backend local. |
| **pnpm** | Gestor de paquetes rápido y eficiente para Node.js. Alternativa a npm/yarn. |
| **JWT** | JSON Web Token. Método estándar de autenticación stateless usado en todos los backends. |
| **MotivoError** | Entidad que categoriza los errores registrados en operaciones de descarga. Ver `motivos-error.model.ts`. |
| **ValoresCalidad** | Atributos de calidad del grano descargado (humedad, proteína, etc.). Ver `valores-calidad.model.ts`. |

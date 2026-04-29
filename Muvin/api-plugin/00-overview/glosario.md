# Glosario — plugin-muvin-cupos

## Términos del dominio

| Término | Definición |
|---|---|
| **Cupo** | Autorización emitida por una cerealera para que un productor pueda entregar un determinado volumen de grano en una planta específica |
| **Carta porte (CP)** | Documento que acompaña el traslado de granos, identifica al productor, el grano, destino y volumen |
| **Cerealera** | Empresa compradora de granos (ACA, Bunge, Cofco, Dreyfus, Samsa, MolinosAgro, Fyo/Tomas Hnos) |
| **CUIT** | Clave Única de Identificación Tributaria — identificador fiscal argentino de personas jurídicas |
| **COE** | Código de Operación Electrónica — identificador único de una carta porte en el sistema AFIP |
| **DescargasRT** | Descargas en tiempo real — proceso que actualiza el estado de descargas de granos usando el COE |
| **Proceso** | Registro en BD que asocia un método PHP a un cliente, definiendo qué lógica ejecutar |
| **Cliente** | Empresa que usa el plugin (tiene sus propias credenciales en Muvin y sus propias cuentas de correo) |
| **ClienteConfiguracion** | Cuenta de correo electrónico IMAP de un cliente, asociada a un proceso |
| **ONCCA** | Ex-Oficina Nacional de Control Comercial Agropecuario — sus tablas de destinos/productos son referencia en el sistema |
| **Muvin API** | API REST de la plataforma Muvin (`https://revision1.muvinapp.com/api/`) a la que se cargan los cupos |
| **Titular** | Productor agropecuario dueño de la carta porte |
| **Rem. com. productor** | Remitente comercial productor — intermediario en la cadena de comercialización |
| **Rem. com. venta primaria** | Remitente comercial de venta primaria |
| **Destinatario** | Empresa que recibe el grano (generalmente la cerealera) |
| **Destino** | Planta o instalación física donde se descarga el grano |
| **Grano/Especie** | Tipo de grano (soja, maíz, trigo, girasol, etc.) |
| **Cosecha** | Campaña agrícola (ej: 2022/23) |

## Términos técnicos del proyecto

| Término | Definición |
|---|---|
| **Ejecutor** | Clase orquestadora que itera todos los clientes activos y ejecuta sus procesos |
| **BaseProcess** | Clase abstracta de PluginV2 que define el contrato de un proceso |
| **MailClient** | Cliente IMAP usado en PluginV2 para conectarse al servidor de correo |
| **EmailDto** | DTO que representa un email descargado (asunto, adjuntos, etc.) |
| **ResultParseDto** | DTO que representa el resultado de parsear un adjunto |
| **ServiciosMuvin** | Singleton que encapsula todas las llamadas a la Muvin API REST |
| **ServiciosDescargas** | Variante de ServiciosMuvin para las operaciones de DescargasRT |
| **LeerPdf** | Clase que parsea PDFs de cupos (extrae texto y datos estructurados) |
| **LeerHtml** | Familia de clases (`LeerHtmlBungue`, `LeerHtmlCofco`, etc.) que parsean HTML de emails |
| **LeerExcel** | Clase que parsea adjuntos Excel |
| **CupoDocumento** | Modelo ActiveRecord de la tabla `cupo_documento` — un cupo parseado pendiente de cargar |
| **DescargasRt** | Modelo ActiveRecord de la tabla `descargas_rt` — una descarga RT a procesar |
| **yii lector** | Comando de consola que dispara `Ejecutor::correrProcesos()` |
| **yii parametros** | Comando de consola que sincroniza catálogos (productos, destinos, oncca, CUITs) |
| **plugin_cupos** | Archivo de cron que ejecuta el plugin cada 15 minutos |

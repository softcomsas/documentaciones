# Glosario

> **Proyecto:** `muvin-ms-worker`
> **Dominio:** Agroindustria argentina / Transferencia de granos

---

## Términos del dominio agroindustrial

| Término | Definición |
|---------|-----------|
| **COE** | Código Único de Operación Electrónica. Identificador asignado por AFIP a cada operación de transferencia de granos registrada en el sistema SENASA. Tiene hasta 12 dígitos. |
| **CUIT** | Clave Única de Identificación Tributaria. Identificador fiscal argentino de 11 dígitos, con formato `XX-XXXXXXXX-X`. Identifica a personas físicas o jurídicas ante AFIP. |
| **Depositario** | Empresa o persona que almacena físicamente el grano en su establecimiento (planta, silo, etc.). Es el tenedor material del grano. |
| **Depositante** | Propietario del grano depositado. Puede ser un productor agropecuario, acopiador, o exportador. |
| **Receptor** | Quien recibe la propiedad del grano en la operación de transferencia. Puede ser la misma entidad que el depositante (autotransferencia) o una entidad diferente. |
| **Cosecha** | Código que identifica la campaña agrícola. Usa 4 dígitos: los dos últimos del año de siembra y los dos últimos del año de cosecha. Ej: `2324` = campaña 2023/2024. |
| **Campaña agrícola** | Período agrícola que abarca desde la siembra hasta la cosecha de los cultivos. En Argentina coincide aproximadamente con el año calendario pero puede cruzar de un año al siguiente. |
| **Certificado de Depósito y Transferencia (CDT)** | Documento oficial emitido por el sistema SENASA que certifica una operación de transferencia de granos depositados. Es el PDF que este worker procesa. |
| **Planta** | Establecimiento físico de almacenamiento de granos (silo, planta de acopio, etc.). Identificado por un número único en el sistema SENASA. |
| **SENASA** | Servicio Nacional de Sanidad y Calidad Agroalimentaria. Organismo gubernamental argentino que regula el comercio de granos. |
| **AFIP** | Administración Federal de Ingresos Públicos. Organismo recaudador argentino que asigna los CUITs y está integrado con el sistema SENASA para validar las operaciones. |

---

## Términos técnicos del sistema

| Término | Definición |
|---------|-----------|
| **DWD** | Domain-wide Delegation. Mecanismo de Google Workspace que permite a una cuenta de servicio (Service Account) impersonar a cualquier usuario de un dominio Google Workspace para acceder a sus recursos (en este caso, su bandeja de Gmail). |
| **Service Account** | Cuenta de servicio de Google Cloud. Tiene una clave privada RSA y puede autenticarse en las APIs de Google en nombre de una aplicación o en nombre de usuarios (con DWD). |
| **JWT (en el contexto de Google)** | JSON Web Token firmado con la clave privada de la Service Account para obtener access tokens de Google OAuth. El SDK de `googleapis` maneja esto automáticamente a través de `google.auth.JWT`. |
| **Bull** | Librería de colas de jobs basada en Redis para Node.js. Permite publicar y consumir tareas asíncronas con garantías de entrega, reintentos y monitoreo. |
| **BullMQ** | Sucesor de Bull con mejor soporte de TypeScript y Redis Clusters. Este worker usa la versión anterior (Bull). |
| **Queue / Cola** | Canal de mensajes Redis donde se publican y consumen los jobs. Este worker procesa la cola `email`. |
| **Process / Proceso Bull** | Subcanal dentro de una cola para distinguir tipos de jobs. La cola `email` tiene el proceso `email.pdf`. |
| **Job** | Unidad de trabajo publicada en la cola. Contiene el payload `IJobEmailPdf` con el ID del mensaje Gmail y las credenciales de autenticación. |
| **MIME** | Multipurpose Internet Mail Extensions. Estándar de formato para emails que permite múltiples partes (texto, HTML, adjuntos) organizadas en árbol. La Gmail API devuelve el email como árbol MIME. |
| **attachmentId** | Identificador único de un adjunto en un mensaje de Gmail. Solo los adjuntos con `attachmentId` pueden descargarse por separado via la API. |
| **base64url** | Variante de base64 que usa `-` y `_` en lugar de `+` y `/`. La Gmail API devuelve los adjuntos en este formato. |
| **pdf-parse** | Librería npm que extrae texto plano de archivos PDF usando el motor PDF.js de Mozilla. |
| **ITransferencia** | Interface TypeScript que representa los datos estructurados extraídos de un CDT. Es el objeto de negocio central de este sistema. |
| **headless worker** | Proceso de Node.js que corre sin servidor HTTP. Este worker usa `NestFactory.create()` + `app.init()` pero nunca llama a `app.listen()`. |

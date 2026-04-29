# Glosario — api-bus

| Término | Definición |
|---|---|
| **Bus de integración** | Componente que centraliza la comunicación entre sistemas internos y externos, abstraendo autenticaciones, protocolos y transformaciones de datos |
| **SiloHub** | Sistema de gestión de operaciones en plantas de acopio de granos. Expone una API REST con OAuth2. URL: `tomascasares.dyndns.org:5052` |
| **CTG** | Código de Trazabilidad de Granos — documento electrónico obligatorio para transportar granos en Argentina (AFIP/SENASA) |
| **Timbúes** | Puerto de descarga sobre el Paraná (Santa Fe). El módulo `timbues` actualiza el CTG de los camiones que ingresan a ese puerto |
| **Viterra** | Empresa agroexportadora con terminales portuarias. El módulo `viterra` gestiona turnos y ventanillas de descarga |
| **CerealTrack** | Sistema de Bayer/Monsanto para rastrear posiciones geográficas de camiones en tiempo real |
| **Zarcam** | Sistema de gestión de pedidos para agroquímicos. Expone API REST en `api.zarcam.com.ar` |
| **San Miguel GPS** | Servicio de seguridad/tracking que provee ubicación de camiones por patente. Credenciales gestionadas con API key en BD |
| **pasamanos** | Método de `SiloHub.php` y `LoginSiloHub.php` que delega el request al sistema externo una vez autenticado. El nombre refleja el patrón proxy |
| **passOpenSSL** | Clave simétrica AES-128-ECB usada para cifrar/descifrar credenciales en tránsito. Configurada por módulo en `main.php` |
| **AES-128-ECB** | Algoritmo de cifrado simétrico usado para proteger credenciales. Modo ECB: ⚠️ no recomendado para producción |
| **BaseCurl** | Componente base (`common\components\BaseCurl`) que abstrae las llamadas HTTP via cURL. Todos los clientes externos lo extienden |
| **ApiRestMuvinController** | Controlador base (`common\components\rest\ApiRestMuvinController`) que centraliza autenticación JWT, CORS y gestión de roles para todos los módulos |
| **PersonaRol** | Modelo que relaciona usuario con rol en el sistema Muvin (ROL_CENTRO, ROL_ADMIN, ROL_DESTINO, etc.) |
| **HtmlPurifier** | Sanitizador de inputs activado en `beforeRequest` para limpiar todos los parámetros GET y POST antes de procesarlos |
| **beforeSend** | Hook de Yii que normaliza todas las respuestas al formato `{success, status, data}` antes de enviarlas al cliente |

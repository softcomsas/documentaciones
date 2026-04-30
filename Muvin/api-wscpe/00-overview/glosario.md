# Glosario — api-wscpe

> [[README]]

| Término | Definición |
|---------|-----------|
| **CPE** | Carta de Porte Electrónica. Documento electrónico obligatorio en Argentina para el traslado de granos. Emitido y controlado por AFIP. |
| **CTG** | Código de Trazabilidad de Granos. Número único asociado a cada CPE. Es el identificador principal para consultas al webservice de AFIP. |
| **AFIP** | Administración Federal de Ingresos Públicos de Argentina. Organismo que emite y controla las CPE a través de sus webservices. |
| **WSCPE** | Webservice de AFIP específico para consulta de Cartas de Porte Electrónicas. Expone métodos SOAP como `ConsultarCPEAutomotor`. |
| **WSAA** | Webservice de Autenticación y Autorización de AFIP. Emite el **Ticket de Acceso (TA)** necesario para consumir cualquier WS de AFIP. |
| **TA** | Ticket de Acceso. Par `{token, sign}` emitido por WSAA con validez temporal. Necesario en cada llamada a WSCPE. |
| **Certificado X.509** | Certificado digital emitido por AFIP para un CUIT específico. Permite la autenticación TLS mutua con WSAA. Compuesto por: `cert` (cadena del certificado), `key` (clave privada), `passphrase`. |
| **CUIT** | Clave Única de Identificación Tributaria. Identificador fiscal argentino de 11 dígitos. |
| **Bearer Token** | Token HTTP enviado en el header `Authorization: Bearer <token>`. Generado por `api-wscpe` tras el login. |
| **Query Cache** | Tabla BD de `api-wscpe` que almacena resultados de consultas previas a AFIP para evitar llamadas repetidas. |
| **Entorno TEST** | Endpoint de homologación de AFIP: `cpea-ws-qaext.afip.gob.ar`. Usado para desarrollo/pruebas. |
| **Entorno PROD** | Endpoint de producción de AFIP: `cpea-ws.afip.gob.ar`. Llamadas reales con datos reales. |
| **`ConsultarCPEAutomotor`** | Método SOAP del WS WSCPE que devuelve los datos completos de una CPE de automotor dado un CTG y CUIT. |
| **Delegación** | Para que un CUIT pueda consultar una CPE que no le pertenece directamente, el titular debe delegarlo en AFIP. Si no está delegado, AFIP devuelve error "no está relacionada con el conjunto". |

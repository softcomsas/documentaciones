# Visión General — api-wscpe

> [[README]]

## ¿Qué es api-wscpe?

`api-wscpe` es un microservicio PHP/Yii2 que actúa como **proxy autenticado** entre los sistemas internos de Muvin y el **Webservice de AFIP `wscpe`** (Carta de Porte Electrónica).

Su función es simple y específica:
1. Recibe una solicitud interna con un **CUIT** y un número de **CTG**.
2. Se autentica contra AFIP usando certificados digitales X.509 (TLS mutuo).
3. Llama al método SOAP `ConsultarCPEAutomotor` del WS de AFIP.
4. Cachea el resultado en la base de datos.
5. Devuelve el resultado como JSON normalizado.

## Contexto de negocio

La **Carta de Porte Electrónica (CPE)** es el documento electrónico obligatorio en Argentina para el traslado de granos. Cada CPE está asociada a un número **CTG** (Código de Trazabilidad de Granos). El sistema Muvin necesita consultar el estado de estas CPE en AFIP para validar operaciones de transporte y carga.

## Usuarios / consumidores del servicio

| Consumidor | Tipo | Descripción |
|-----------|------|-------------|
| `api` (NestJS) | Interno | Backend principal Muvin |
| `api-bus` (PHP Yii2) | Interno | Backend bus de operaciones |
| Administradores | Via Swagger UI | Pueden cargar certificados y crear usuarios |

## Números del sistema

| Métrica | Valor |
|---------|-------|
| Endpoints REST expuestos | 5 |
| Tablas en BD | 3 (`user`, `query_cache`, `certificado`) |
| Controllers | 5 (Login, Cpe, Certificado, User, Site) |
| Componentes AFIP | 2 (`Afip.php`, `Afipws.php`) |
| Entornos AFIP | 2 (TEST: `cpea-ws-qaext.afip.gob.ar` / PROD: `cpea-ws.afip.gob.ar`) |

## Restricciones conocidas

- Solo opera con el webservice `wscpe` de AFIP. No consulta otros WS (wsfe, wslum, etc.).
- El certificado digital X.509 (CUIT + cert + key + passphrase) debe ser cargado manualmente por un usuario `admin`.
- El caché de resultados no tiene TTL configurado: una CTG consultada queda guardada indefinidamente hasta que se borre manualmente o la BD se limpie.
- El entorno (`TEST` vs producción) se controla con la variable `ENTORNO_WS` en `params.php`. Por defecto está en `TEST`.

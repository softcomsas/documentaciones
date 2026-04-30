# Módulo: Afipws (Componente SOAP AFIP)

> **Archivos:** `components/Afipws.php`, `components/Afip.php`
> **Criticidad:** 🔴 Crítica — integración con AFIP

## Propósito

`Afipws` es el componente central que encapsula toda la comunicación con AFIP:
1. Obtiene el Ticket de Acceso (TA) del WSAA usando el certificado X.509.
2. Llama al método SOAP `ConsultarCPEAutomotor` del WSCPE.
3. Convierte la respuesta `stdClass` en array PHP puro.

## Afipws::conectar()

```php
static public function conectar(int $id_user): TA|false
```

1. Descarga el certificado de BD via `Certificado::descargarCertificado($id_user)`.
2. Escribe `cert.key` y `key.pem` en `Afip_res/` en disco.
3. Instancia `Afip` con CUIT, cert, key, passphrase y carpetas de recursos.
4. Llama `$afip->GetServiceTA("wscpe")` → devuelve `{token, sign}`.
5. El entorno (TEST/PROD) se lee de `Yii::$app->params['ENTORNO_WS']`.

## Afipws::ConsultarCPE()

```php
static public function ConsultarCPE(string $token, string $sign, $cuit, $ctg): array
```

1. Instancia `SoapClient` con la WSDL del WSCPE (TEST o PROD).
2. Construye el request con `auth` (token, sign, cuitRepresentada) y `solicitud` (nroCTG).
3. Llama `$client->ConsultarCPEAutomotor($params)->respuesta`.
4. Elimina el campo `pdf` del resultado.
5. Convierte recursivamente `stdClass → array` con `convertirStdClassToArray()`.
6. Lanza `\Exception` si hay `SoapFault`.

## Afip.php (WSAA Client)

Clase que maneja el intercambio con el WSAA de AFIP:
- Lee los certificados X.509 del directorio `Afip_res/`.
- Genera y firma el XML de autenticación.
- Cachea el TA en `Afip_TA/` para reutilizarlo mientras sea válido.
- Soporta entorno TEST y PROD.

## Manejo de errores de AFIP

| Caso de error | Comportamiento |
|--------------|----------------|
| CUIT no delegado | Devuelve string `"El cuit X no se puede consultar (no delegado)"` |
| Error genérico AFIP | Devuelve string `"Error en el WEBSERVICE - {mensaje}"` |
| SoapFault | Relanza como `\Exception` hacia `QueryCache` |

## Riesgos

- 🔴 El certificado se escribe en disco en cada consulta. Si hay concurrencia alta, pueden producirse condiciones de carrera al sobrescribir `cert.key`.
- ⚠️ No hay reintentos automáticos si AFIP devuelve error transitorio.
- ⚠️ `SoapClient` no tiene timeout configurado explícitamente. Una consulta lenta a AFIP puede colgar el proceso PHP indefinidamente.

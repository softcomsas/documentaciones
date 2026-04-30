# F-02 · Consulta de CPE por CTG + CUIT

> **Módulo:** [modulo-cpe](../01-modulos/modulo-cpe.md)
> **Endpoint:** `POST /cpe`

## Descripción

Función principal del servicio. Dado un par `(CUIT, CTG)`, devuelve los datos completos de la Carta de Porte Electrónica consultando a AFIP (con caché). El resultado incluye: partes intervinientes, mercadería, transporte, fechas y estado de la CPE.

## Flujo completo

Ver [flujo-consulta-cpe](../06-flujos-transversales/flujo-consulta-cpe.md).

## Casos de uso

| Caso | Descripción | Resultado |
|------|-------------|-----------|
| CTG en caché | El CTG ya fue consultado previamente para ese CUIT | Devuelve dato de BD sin llamar a AFIP |
| CTG nuevo | Primera consulta para ese par CTG+CUIT | Llama a AFIP, cachea y devuelve |
| CUIT no delegado | El CUIT no tiene acceso a esa CPE en AFIP | Error descriptivo en `data` |
| Error AFIP | Fallo del WS de AFIP | Mensaje de error en `data` |
| TA expirado | El Ticket de Acceso del certificado expiró | `Afip.php` renueva automáticamente el TA |

## Observaciones sobre el resultado

- El campo `pdf` de la respuesta AFIP es **eliminado** antes de devolver (puede ser grande).
- El resultado es un array de 1 elemento: `data: [{ datos CPE }]`.
- Los campos del objeto CPE corresponden a la respuesta SOAP del método `ConsultarCPEAutomotor` de AFIP. La estructura exacta depende del tipo de CPE (automotor, ferroviario, etc.).

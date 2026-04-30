# F-04 · Gestión de Certificado AFIP

> **Módulo:** [modulo-certificado](../01-modulos/modulo-certificado.md)
> **Endpoint:** `POST /certificado`

## Descripción

Permite al administrador cargar el certificado X.509 necesario para autenticarse ante el WSAA de AFIP. Cada carga reemplaza el certificado existente.

## Cuándo se necesita

- Primera configuración del sistema.
- Renovación del certificado (los certificados de AFIP tienen vencimiento, usualmente 2-3 años).
- Cambio de CUIT operador.

## Procedimiento de carga

1. Obtener certificado de AFIP (solicitar en el portal de AFIP con el CUIT operador).
2. Preparar los 4 datos: `cuit`, texto del `cert` (PEM), `passphrase` y texto del `key` (PEM).
3. Hacer `POST /certificado` con Bearer token del usuario `admin`.
4. Verificar respuesta exitosa.
5. Probar con `POST /cpe` que la conexión a AFIP funciona.

Ver detalles en [modulo-certificado](../01-modulos/modulo-certificado.md).

# Módulo: CertificadoController

> **Archivo:** `controllers/CertificadoController.php`
> **Ruta base:** `/certificado`
> **Auth:** ✅ Bearer token + verificación `username == 'admin'`
> **Criticidad:** 🔴 Alta — gestiona credenciales de acceso a AFIP

## Propósito

Permite cargar o reemplazar el certificado digital (X.509) usado para autenticarse ante AFIP. Solo accesible por el usuario `admin`. El certificado queda almacenado en la tabla `certificado` de la BD.

## Acciones

| Acción | Verbo | Ruta | Descripción |
|--------|-------|------|-------------|
| `actionIndex` | POST | `/certificado` | Cargar o reemplazar certificado |

## actionIndex — Detalle

**Validación de rol:**
```php
if(Yii::$app->user->identity->username != 'admin') → 425 "No tiene permisos"
```

**Request:**
```json
{
  "cuit": "20123456789",
  "cert": "-----BEGIN CERTIFICATE-----\n...",
  "passphrase": "mi_passphrase",
  "key": "-----BEGIN RSA PRIVATE KEY-----\n..."
}
```

**Lógica:**
1. Valida que el usuario sea `admin`.
2. Crea un `Certificado` model y carga los parámetros.
3. Fija `id_user = 1`.
4. Inicia transacción → `borrarCertificado()` (elimina el existente) → `save()` (guarda el nuevo).
5. Hace commit o rollback si hay error.

**Response exitosa (200):**
```json
{
  "success": true,
  "status": 200,
  "data": "Certificado cargado/actualizado correctamente"
}
```

## Riesgos

- 🔴 El certificado privado (`key`) y la `passphrase` se almacenan en texto plano en la base de datos. Ver [SEC-02](../05-inventarios/security-inventory.md).
- 🔴 El certificado se escribe en disco en `Afip_res/cert.key` y `Afip_res/key.pem` como archivos temporales. Si el directorio es accesible por web, las credenciales quedan expuestas.
- ⚠️ Control de acceso por `username == 'admin'` hardcodeado en código. No usa el RBAC de Yii2.
- ⚠️ El `id_user` está fijo en `1`. Solo hay un slot de certificado en todo el sistema.

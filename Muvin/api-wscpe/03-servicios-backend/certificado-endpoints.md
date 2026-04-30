# Endpoints: Certificado y Usuario

> [[_indice-servicios]]

## POST `/certificado`

**Auth:** ✅ Bearer + usuario `admin`

**Request:**
```json
{
  "cuit": "20123456789",
  "cert": "-----BEGIN CERTIFICATE-----\nMIID...\n-----END CERTIFICATE-----",
  "passphrase": "mi_passphrase_secreta",
  "key": "-----BEGIN RSA PRIVATE KEY-----\nMIIE...\n-----END RSA PRIVATE KEY-----"
}
```

**Response 200:**
```json
{
  "success": true,
  "status": 200,
  "data": "Certificado cargado/actualizado correctamente"
}
```

**Response 425 — Sin permisos:**
```json
{
  "success": false,
  "status": 425,
  "data": "No tiene permisos para esta accion."
}
```

---

## POST `/user/create-user`

**Auth:** ✅ Bearer + usuario `admin`

**Request:**
```json
{
  "username": "nuevo_usuario",
  "password": "contraseña_segura",
  "email": "usuario@example.com"
}
```

**Response 200:**
```json
{
  "success": true,
  "status": 200,
  "data": "Usuario creado éxitosamente."
}
```

**Response 425 — Sin permisos o error de validación:**
```json
{
  "success": false,
  "status": 425,
  "data": { "username": ["..."] }
}
```

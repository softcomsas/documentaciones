# Flujo: Carga de Certificado AFIP

> [[_indice-flujos]] | Módulo: [[modulo-certificado]]

## Secuencia

```mermaid
sequenceDiagram
    participant Admin
    participant API as LoginController / CertificadoController
    participant DB as MySQL
    participant FS as Filesystem (Afip_res/)

    Admin->>API: POST /login/login {username, password}
    API->>DB: SELECT * FROM user WHERE username=?
    DB-->>API: user (role=admin)
    API-->>Admin: { access_token }

    Admin->>API: POST /certificado {cert, key, passphrase}
    Note over API: BearerAuth + verifyAdmin()

    API->>DB: Certificado::find()->where(['id_user'=>1])
    alt Certificado existente
        API->>DB: DELETE FROM certificado WHERE id_user=1
        API->>FS: unlink(cert.key), unlink(key.pem)
    end

    API->>DB: INSERT INTO certificado (cert, key, passphrase, id_user=1)
    DB-->>API: id

    API->>FS: file_put_contents('Afip_res/cert.key', cert)
    API->>FS: file_put_contents('Afip_res/key.pem', key)
    API-->>Admin: { success:true, status:201 }

    Note over Admin,FS: Verificar con POST /cpe para confirmar que el certificado es válido
```

## Efectos en el sistema

| Efecto | Detalle |
|--------|---------|
| BD actualizada | Tabla `certificado` reemplazada para `id_user = 1` |
| Archivos reemplazados | `Afip_res/cert.key` y `Afip_res/key.pem` sobrescritos |
| Caché de TA invalidado | `Afip_TA/TA.xml` sigue vigente hasta TTL WSAA, pero el próximo request lo renovará automáticamente si cambiaron los credenciales |

## Riesgos operativos

- **Condición de carrera:** Si dos requests `/cpe` llegan mientras se sube el certificado, pueden leer archivos incompletos.
- **Sin rollback:** Si el `file_put_contents` falla, la BD ya contiene el nuevo certificado pero el archivo en disco es el anterior.
- **Passphrase en texto plano:** Almacenada directamente en la tabla `certificado`.

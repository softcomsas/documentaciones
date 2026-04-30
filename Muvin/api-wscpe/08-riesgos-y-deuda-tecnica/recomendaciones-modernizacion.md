# Recomendaciones de Modernización

> [[deuda-tecnica]] | [[hotspots]]

## Fase 1 — Seguridad inmediata (sin cambios de arquitectura)

### R-01: Reemplazar generación de token
```php
// Antes
$token = md5(uniqid());

// Después
$token = bin2hex(random_bytes(32));
```

### R-02: Mover cookieValidationKey a variable de entorno
```php
// config/web.php
'cookieValidationKey' => getenv('COOKIE_VALIDATION_KEY'),
```

### R-03: Agregar TTL al caché de CTG
Agregar columna `expires_at DATETIME` a `query_cache` y verificar en `buscarCtg()`:
```php
if ($cache && $cache->expires_at > date('Y-m-d H:i:s')) {
    return $cache->data;
}
```

### R-04: Timeout en SoapClient
```php
$options = [
    'connection_timeout' => 10,
    'default_socket_timeout' => 30,
];
$client = new SoapClient($wsdl, $options);
```

### R-05: HTTP correcto para errores AFIP
```php
if ($afipError) {
    return $this->asJson(['success' => false, 'error' => $mensaje])->setStatusCode(502);
}
```

---

## Fase 2 — Fiabilidad y operación

### R-06: File lock al escribir certificados
```php
$fp = fopen('Afip_res/cert.key', 'w');
flock($fp, LOCK_EX);
fwrite($fp, $cert);
flock($fp, LOCK_UN);
fclose($fp);
```

### R-07: Transacción BD + disco atómica
```php
$transaction = Yii::$app->db->beginTransaction();
try {
    $certificado->save();
    file_put_contents('Afip_res/cert.key', $cert);
    file_put_contents('Afip_res/key.pem', $key);
    $transaction->commit();
} catch (\Exception $e) {
    $transaction->rollBack();
    throw $e;
}
```

### R-08: Rate limiting en login
Usar `yii2-ratelimiter` o middleware Nginx (`limit_req_zone`) para máximo 5 intentos/minuto por IP.

---

## Fase 3 — Arquitectura (largo plazo)

### R-09: Migrar a PHP 8.2+
- Tipos de retorno estrictos, `readonly` properties, fibers para async.
- Yii2 soporta PHP 8.x desde versión 2.0.43.

### R-10: Multi-tenancy real
- Eliminar `id_user = 1` hardcodeado.
- Asociar `certificado` y `query_cache` al usuario autenticado via `Yii::$app->user->id`.

### R-11: Separar capa de caché
- Mover `query_cache` a Redis con TTL nativo (`SETEX`).
- Eliminar tabla MySQL `query_cache`.

### R-12: Migrar certificados a almacenamiento seguro
- Usar HashiCorp Vault o AWS Secrets Manager para almacenar `cert` y `key`.
- Nunca escribir la clave privada en disco local.

### R-13: OpenAPI 3 + validación de contratos
- Reemplazar `light/yii2-swagger` (Swagger 2.0) por `zircote/swagger-php` (OpenAPI 3.x).
- Agregar validación de request contra esquema OpenAPI en un middleware.

---

## Resumen de esfuerzo

| Fase | Items | Esfuerzo total estimado | Impacto |
|------|-------|------------------------|---------|
| 1 — Seguridad inmediata | R-01 a R-05 | 1–2 días | 🔴 Crítico |
| 2 — Fiabilidad | R-06 a R-08 | 3–5 días | 🟠 Alto |
| 3 — Arquitectura | R-09 a R-13 | 3–6 semanas | 🟡 Estratégico |

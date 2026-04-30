# Requisitos de Entorno

> [[_indice-servicios]] | [[configuracion]]

## Software base

| Componente | Versión requerida | Notas |
|------------|-------------------|-------|
| PHP | >= 7.4 | EOL nov-2022; migrar a 8.x recomendado |
| MySQL | >= 5.7 / 8.x | Base de datos principal |
| Composer | >= 2.x | Gestión de dependencias PHP |
| Docker (opcional) | >= 20.x | `docker-compose.yml` incluido |

## Extensiones PHP obligatorias

| Extensión | Uso |
|-----------|-----|
| `soap` | SoapClient para AFIP WSCPE |
| `openssl` | Firma WSAA, lectura de certificados PEM |
| `pdo_mysql` | Conexión ActiveRecord → MySQL |
| `json` | Request/Response REST |
| `mbstring` | Yii2 string helpers |
| `fileinfo` | Validaciones de archivos |

Verificar con:
```bash
php -m | Select-String "soap|openssl|pdo_mysql|json|mbstring"
```

## Permisos de directorios

```text
source/
  Afip_res/   → 0777 (escritura de cert.key y key.pem)
  Afip_TA/    → 0777 (escritura de TA.xml — Ticket de Acceso WSAA)
  web/        → lectura pública
```

## Instalación inicial

```bash
# Instalar dependencias
composer install --no-dev

# Ejecutar migraciones
php yii migrate

# (Opcional) Poblar usuario admin inicial
# INSERT INTO user (username, password_hash, role, ...) VALUES (...)
```

## Docker (desarrollo)

```bash
docker-compose up -d
# La aplicación queda en http://localhost:<puerto>
```

## Conectividad externa requerida

| Host | Puerto | Propósito |
|------|--------|-----------|
| `wsaahomo.afip.gov.ar` | 443 | WSAA (TEST) |
| `wsaa.afip.gov.ar` | 443 | WSAA (PROD) |
| `cpea-ws-qaext.afip.gob.ar` | 443 | WSCPE (TEST) |
| `cpea-ws.afip.gob.ar` | 443 | WSCPE (PROD) |

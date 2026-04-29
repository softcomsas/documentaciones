# Entidad: SanMiguelKey

> **Módulo:** `agroquimicos`
> **Modelo:** `modules/agroquimicos/models/SanMiguelKey.php`
> **Tipo:** ActiveRecord Yii 2 — tabla **propia** de api-bus (R/W)

---

## Propósito

Almacena las claves/tokens de autenticación utilizados para comunicarse con el sistema GPS de **San Miguel** (`securityconsultant.com.ar`). La API de SanMiguel requiere una clave de acceso que se obtiene y renueva dinámicamente; este modelo persiste esa clave para reutilizarla entre requests.

---

## Esquema de tabla

**Tabla:** `agroq_san_miguel_key`

| Columna | Tipo | Constraints | Descripción |
|---------|------|-------------|-------------|
| `id` | INT | PK, AUTO_INCREMENT | Identificador |
| `key` | VARCHAR(255) | NOT NULL | Token/clave de autenticación SanMiguel |

**Migration:** `m220711_134828_create_agroq_san_miguel_key_table.php`

```sql
CREATE TABLE agroq_san_miguel_key (
    id    INT          NOT NULL AUTO_INCREMENT,
    key   VARCHAR(255) NOT NULL,
    PRIMARY KEY (id)
);
```

---

## Uso en código

```php
// Obtener la clave actual
$keyRecord = SanMiguelKey::findOne(1);
$key = $keyRecord->key;

// Actualizar la clave
$keyRecord->key = $nuevoToken;
$keyRecord->save();
```

---

## Riesgos

| Riesgo | Severidad | Descripción |
|--------|-----------|-------------|
| Columna llamada `key` | 🟡 Media | `key` es palabra reservada en MySQL; puede causar errores sin backticks |
| Sin timestamp de expiración | 🔴 Alta | No hay campo para saber cuándo expiró el token |
| Sin historial de rotación | 🟡 Media | No hay log de cambios de clave |
| Tabla con un solo registro | 🟡 Media | Diseño asume un único cliente SanMiguel |

---

## Referencias

- [[modulo-agroquimicos]]
- [[agroquimicos-endpoints]]
- [[security-inventory]]

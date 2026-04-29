# Endpoints — Módulo Agroquímicos

> **Controlador:** `modules/agroquimicos/controllers/PedidoController.php`
> **Base URL:** `/agroquimicos/pedido/`
> **Autenticación:** JWT Bearer

---

## GET `/agroquimicos/pedido/{id}`

**Descripción:** Obtiene información completa de un pedido de agroquímicos combinando datos de Zarcam (pedido comercial) y SanMiguel GPS (posición del camión).

**Flujo:**

```mermaid
flowchart TD
    A[Cliente] -->|GET /pedido/123 + JWT| B[PedidoController\nactionView]
    B --> C[PedidosById UseCase]
    C --> D[Zarcam::getPedido\nid_pedido]
    C --> E[SanMiguelKey::findOne\nDB local]
    D -->|pedido comercial| F[Merge resultado]
    E -->|GPS key| G[SanMiguel::getPosicion\ncamion]
    G -->|posición GPS| F
    F -->|{pedido + GPS}| B
    B -->|{success, status, data}| A
```

**Path Params:**

| Param | Tipo | Descripción |
|-------|------|-------------|
| `id` | integer | ID del pedido en Zarcam |

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "id_pedido": 123,
    "producto": "Glifosato",
    "cantidad": 500,
    "estado": "EN_CAMINO",
    "camion": {
      "patente": "ABC123",
      "latitud": -34.123,
      "longitud": -58.456,
      "velocidad": 80
    }
  }
}
```

---

## GET `/agroquimicos/pedido/patente/{patente}`

**Descripción:** Consulta la posición GPS actual de un camión directamente en el sistema SanMiguel GPS, dado su número de patente.

**Flujo:**

```mermaid
flowchart LR
    A[Cliente] -->|GET /patente/ABC123 + JWT| B[PedidoController\nactionPatente]
    B --> C[SanMiguelKey::findOne\nDB local → GPS key]
    C --> D[SanMiguel::getPosicionByPatente\npatente]
    D -->|HTTPS desa3.securityconsultant.com.ar:8081| E[SanMiguel GPS]
    E -->|posición| D
    D --> B
    B -->|{success, status, data}| A
```

**Path Params:**

| Param | Tipo | Descripción |
|-------|------|-------------|
| `patente` | string | Patente del camión (ej: `AB123CD`) |

**Response:**
```json
{
  "success": true,
  "status": 200,
  "data": {
    "patente": "AB123CD",
    "latitud": -34.456,
    "longitud": -58.789,
    "velocidad": 0,
    "timestamp": "2024-01-15T14:22:00",
    "estado": "DETENIDO"
  }
}
```

> ⚠️ La URL base de SanMiguel GPS es `https://desa3.securityconsultant.com.ar:8081` — el prefijo `desa3` sugiere entorno de desarrollo. Verificar si es la URL de producción.

---

## Componentes internos

| Componente | Rol |
|---|---|
| `PedidoController` | Controlador con 2 acciones (`view`, `patente`) |
| `PedidosById` | UseCase que orquesta Zarcam + SanMiguel |
| `Zarcam` | Cliente HTTP hacia `zarcam.com.ar` |
| `SanMiguel` | Cliente HTTP hacia SanMiguel GPS |
| `SanMiguelKey` | ActiveRecord para tabla `agroq_san_miguel_key` |
| `BaseCurl` | Cliente HTTP base (SSL deshabilitado ⚠️) |

---

## Configuración requerida

En `config/main.php`:

```php
'urlBaseZarcam'     => 'https://zarcam.com.ar/...',
'urlBaseSanMiguel'  => 'https://desa3.securityconsultant.com.ar:8081',
'usernameZarcam'    => 'admin',   // ⚠️ hardcoded
'secretZarcam'      => 'admin',   // ⚠️ hardcoded
```

---

## Referencias

- [[modulo-agroquimicos]]
- [[f07-agroquimicos-pedidos]]
- [[entidad-san-miguel-key]]

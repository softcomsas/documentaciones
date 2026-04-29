# Endpoints — Módulo Timbúes

> **Controlador:** `modules/timbues/controllers/TimbuesController.php`
> **Base URL:** `/timbues/timbues/`
> **Autenticación:** JWT Bearer

---

## POST `/timbues/timbues/actualizar-ctg`

**Descripción:** Actualiza la Carta de Porte (CTG) en el sistema de Puerto Timbúes.

**Flujo:**

```mermaid
sequenceDiagram
    participant C as Cliente
    participant T as TimbuesController
    participant TC as Timbues (component)
    participant LT as LoginTimbues
    participant EXT as tramitesenlinea.com.ar

    C->>T: POST body cifrado AES
    T->>T: desencriptarParametros()
    T->>TC: actualizarCtg(params)
    TC->>LT: getToken()
    LT->>EXT: POST /login
    EXT-->>LT: token
    TC->>EXT: PUT /ctg/{id}
    EXT-->>TC: resultado
    TC-->>T: resultado
    T-->>C: {success, status, data}
```

**Request Body (cifrado AES-128-ECB):**

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `ctg` | string | Número de CTG a actualizar |
| `login` | string | Usuario Timbúes |
| `...` | mixed | Campos adicionales del CTG |

**Response exitosa:**
```json
{
  "success": true,
  "status": 200,
  "data": { "ctg": "...", "estado": "CONFIRMADO" }
}
```

**Errores posibles:**

| Código | Descripción |
|--------|-------------|
| 401 | Token JWT inválido o expirado |
| 500 | Error al conectar con tramitesenlinea.com.ar |
| 422 | Validación de parámetros fallida |

---

## Componentes internos

| Componente | Rol |
|---|---|
| `TimbuesController` | Controlador con acción `actualizarCtg` |
| `Timbues` | Facade de integración con Timbúes |
| `LoginTimbues` | Gestión de autenticación con puerto Timbúes |
| `BaseCurl` | Cliente HTTP (SSL deshabilitado ⚠️) |

---

## Referencias

- [[modulo-timbues]]
- [[f04-timbues-actualizar-ctg]]

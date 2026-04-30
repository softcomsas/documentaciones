# F-03 · Caché de Resultados AFIP

> **Módulo:** [modulo-cpe](../01-modulos/modulo-cpe.md)
> **Clase:** `models/QueryCache.php`

## Descripción

`QueryCache` es el modelo AR que actúa como caché de consultas a AFIP. Evita llamadas repetidas al WS externo para el mismo par `(CTG, CUIT)`.

## Lógica de caché

```
buscarCtg(ctg, cuit, userId)
  │
  ├─► SELECT * FROM query_cache WHERE ctg=X AND id_user=1 AND cuit_consulta=Y
  │
  ├─ Si NO existe → consultarCtg() → INSERT → devolver resultado
  │
  └─ Si existe → verificar que el CUIT aparece recursivamente en result
       ├─ Si SÍ aparece → devolver cache (json_decode)
       └─ Si NO aparece → consultarCtg() → INSERT → devolver resultado
```

## Tabla `query_cache`

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | INT PK | Autoincremental |
| `created_at` | DATETIME | Fecha de la consulta |
| `ctg` | VARCHAR(70) | Número de CTG |
| `result` | TEXT/JSON | Resultado serializado de AFIP |
| `id_user` | INT FK | Siempre `1` (hardcodeado) |
| `cuit_consulta` | VARCHAR(11) | CUIT que se usó para consultar |

## Riesgos

- ⚠️ **Sin TTL.** Los datos de AFIP pueden cambiar (una CPE puede anularse, modificarse su estado). El caché devolverá datos desactualizados indefinidamente.
- ⚠️ `id_user` siempre es `1`. No hay segregación de caché por usuario.
- ⚠️ La verificación de presencia del CUIT usa búsqueda recursiva en el resultado JSON — puede ser lenta para respuestas grandes.
- ⚠️ No hay endpoint para invalidar/limpiar el caché manualmente.

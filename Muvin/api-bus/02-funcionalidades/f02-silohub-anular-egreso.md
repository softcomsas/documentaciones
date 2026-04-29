# F-02 — Anular Egreso Planta (SiloHub)

> **Módulo:** [[modulo-silohub]]
> **Tipo:** 🔌 Integración
> **Endpoint de entrada:** `POST /silohub/algoritmo/anular-egreso-planta`

## Descripción funcional

Cancela un egreso de grano previamente registrado en SiloHub. Mismo patrón que F-01: descifra AES, autentica OAuth2 y reenvía a `/api/ConsultaSiloHub/AnularEgresoPlanta`.

## Servicios backend invocados

| Verbo | Ruta | Descripción |
|---|---|---|
| POST | `/api/ConsultaSiloHub/AnularEgresoPlanta` | Cancela el egreso |

---

# F-03 — Sincronización de Catálogos SiloHub

> **Módulo:** [[modulo-silohub]]
> **Tipo:** 🔄 Consulta/Sincronización
> **Endpoints de entrada:** múltiples (ver tabla)

## Descripción funcional

Conjunto de endpoints de consulta que sincronizan catálogos desde SiloHub hacia el sistema Muvin. Todos siguen el mismo patrón: descifrar AES → autenticar → GET/POST al endpoint SiloHub correspondiente.

## Endpoints expuestos

| Acción | Endpoint api-bus | Endpoint SiloHub | Método |
|---|---|---|---|
| Entidades clientes | `POST /silohub/algoritmo/get-entidades-clientes` | `/api/Sincronizacion/GetRecursos/SH-ENTIDADES-CLIENTES?page=0&size=10000` + page=1 | GET (2 páginas combinadas) |
| Campos productor | `POST /silohub/algoritmo/get-campos-productor` | `/api/ConsultaSiloHub/GetCamposProductor` | POST |
| Productos/especies | `POST /silohub/algoritmo/get-productos` | `/api/Especie/GetEspecies` | POST |
| Contratos | `POST /silohub/algoritmo/get-contratos` | `/api/ConsultaSiloHub/GetListaContratos` | POST |
| Plantas | `POST /silohub/algoritmo/get-plantas` | `/api/Sincronizacion/GetRecursos/PLANTAS?page=0&size=10000` | GET |
| Saldo productor | `POST /silohub/algoritmo/get-saldo-productor-especie-cosecha` | `/api/Especie/GetExistenciaPorEspeciesCosecha` | POST |
| Choferes/transportistas | `POST /silohub/algoritmo/get-choferes-algoritmo` | `/api/Sincronizacion/GetRecursos/SH-ENTIDADES-TRANSPORTISTAS?page=0&size=10000` | GET |
| Modificar egreso | `POST /silohub/algoritmo/get-modificar-egreso` | `/api/ConsultaSiloHub/GetModificarEgresoSH` | POST |

## Nota sobre paginación (GetEntidadesClientes)

```php
// Hace 2 llamadas y combina Items[]
$items0 = $res0['mensaje']['Items'];  // page=0
$items1 = $res1['mensaje']['Items'];  // page=1
$itemsCombinados = array_merge($items0, $items1);
```
No usa paginación dinámica — asume que con 2 páginas de 10.000 items alcanza. ⚠️ Riesgo si crece la cantidad de entidades.

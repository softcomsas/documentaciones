# Índice de Servicios Backend — api-bus

> **Última revisión:** 2026-04-29

Listado de todos los endpoints expuestos por api-bus, agrupados por módulo.

---

## Resumen de endpoints

| # | Módulo | Método | Ruta | Acción |
|---|--------|--------|------|--------|
| 1 | silohub | POST | `/silohub/algoritmo/emitir-pedido-logistica` | Emitir pedido en SiloHub |
| 2 | silohub | POST | `/silohub/algoritmo/anular-egreso-planta` | Anular egreso en SiloHub |
| 3 | silohub | GET | `/silohub/algoritmo/get-entidades-clientes` | Catálogo de entidades/clientes |
| 4 | silohub | GET | `/silohub/algoritmo/get-campos-productor` | Campos del productor |
| 5 | silohub | GET | `/silohub/algoritmo/get-productos` | Catálogo de productos |
| 6 | silohub | GET | `/silohub/algoritmo/get-contratos` | Contratos del productor |
| 7 | silohub | GET | `/silohub/algoritmo/get-plantas` | Plantas disponibles |
| 8 | silohub | GET | `/silohub/algoritmo/get-saldo-productor-especie-cosecha` | Saldos del productor |
| 9 | silohub | GET | `/silohub/algoritmo/get-choferes-algoritmo` | Choferes registrados |
| 10 | silohub | POST | `/silohub/algoritmo/get-modificar-egreso` | Modificar egreso existente |
| 11 | timbues | POST | `/timbues/timbues/actualizar-ctg` | Actualizar CTG en Timbúes |
| 12 | viterra | GET | `/viterra/destino/situacion-puerto` | Situación de turnos en puerto |
| 13 | viterra | GET | `/viterra/destino/informacion-ventanilla/{id}` | Info de ventanilla por ID |
| 14 | monsanto | GET | `/monsanto/login/cereal-track` | Posiciones GPS CerealTrack |
| 15 | agroquimicos | GET | `/agroquimicos/pedido/{id}` | Pedido Zarcam + GPS SanMiguel |
| 16 | agroquimicos | GET | `/agroquimicos/pedido/patente/{patente}` | Posición GPS por patente |

---

## Autenticación global

Todos los endpoints (salvo configuración explícita) requieren:

```http
Authorization: Bearer <JWT>
```

El JWT es validado en `ApiRestMuvinController::behaviors()` mediante el comportamiento `authenticator` de Yii 2 REST.

---

## Archivos de detalle

- [[silohub-endpoints]]
- [[timbues-endpoints]]
- [[viterra-endpoints]]
- [[monsanto-endpoints]]
- [[agroquimicos-endpoints]]

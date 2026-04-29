# F-02 — Carta Porte Camión

## Descripción

Wizard multi-paso para generar una **Carta de Porte Electrónica (CPE) para transporte en camión**. Permite al usuario buscar un cupo activo, completar los datos del viaje (transportista, chofer, patente, destino, atributos de calidad) y enviar la solicitud a AFIP/SCE.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (página) | `descargas-app/frontend/src/app/page/carta-porte/` |
| Frontend (wizard) | `descargas-app/frontend/src/app/components/carta-porte-wizard/` |
| Frontend (búsqueda) | `descargas-app/frontend/src/app/components/carta-porte-search/` |
| Backend | `descargas-app/backend/src/controllers/descargas.controller.ts` |
| Servicio FE | `carta-porte/services/descarga-carta-porte.service.ts` |

## Ruta Angular

`/descargas/carta-porte`

## Flujo del wizard

```
1. CartaPorteSearchComponent
   └─ Buscar cupo por CTG o parámetros
   └─ Seleccionar cupo activo

2. CartaPorteWizardComponent
   ├─ Paso 1: Datos del productor / remitente
   ├─ Paso 2: Datos del transportista, chofer, patente
   ├─ Paso 3: Destino y planta
   ├─ Paso 4: Atributos de calidad del grano
   └─ Paso 5: Confirmación y envío a AFIP
```

## Componentes Angular

- `CartaPorteSearchComponent` — búsqueda de cupo
- `CartaPorteWizardComponent` — wizard principal
- `CartaPorteWizardCcppComponent` — variante CCPP (Control de Carga Por Peso)
- `ProvinciaLocalidadService` — catálogo de provincias/localidades
- `AtributoCalidadService` — catálogo de atributos de calidad

## Endpoints backend consumidos

| Método | Ruta | Descripción |
|---|---|---|
| `GET` | `/descargas` | Buscar cupo por filtros |
| `POST` | `/descargas` | Crear carta porte |
| `PUT` | `/descargas` | Actualizar estado de carta porte |

## Rol requerido

`DES Verificador CP` (o similar, según guards de la ruta)

## Validaciones

- Datos de chofer (CUIT, nombre, licencia)
- Patente del camión (formato)
- Atributos de calidad obligatorios según el tipo de grano
- No se puede emitir carta porte sobre un cupo ya utilizado o vencido

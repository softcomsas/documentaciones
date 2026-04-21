# Entidad: Comprador

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Descripción

Entidad del sistema legacy que representa a una persona jurídica o física que actúa como **compradora** en el sistema Muvin. Este microservicio solo expone un subconjunto reducido de sus campos.

## Forma en el backend legacy (respuesta cruda)

Definida en `src/api/queries/comprador-by-razon-social.ts` como `IRes`:

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | `number` | Identificador único del comprador en el sistema legacy |
| `razonSocial` | `string` | Nombre o razón social de la empresa/persona |
| `cuitCuil` | `string` | CUIT o CUIL con guiones (ej. `30-12345678-9`) |

> [!warning] Campos no expuestos
> El backend legacy puede retornar más campos. Solo se documentan los que están tipados en el microservicio. Los demás son descartados por el adapter.

## Forma normalizada (expuesta a consumidores)

Definida en `src/contracts/ms-legacy/interfaces/comprador-by-razon-social.ts` como `IElement`:

| Campo | Tipo | Origen | Descripción |
|-------|------|--------|-------------|
| `id` | `number` | `id` | Sin transformación |
| `rs` | `string` | `razonSocial` | Renombrado por el adapter |
| `cuit` | `string` | `cuitCuil` | Renombrado por el adapter |

## Paginación

La respuesta del backend legacy incluye metadatos de paginación que **no se propagan**:

| Campo legacy | Tipo | Descripción | ¿Propagado? |
|-------------|------|-------------|-------------|
| `meta.total` | `number` | Total de registros que coinciden con la búsqueda | ❌ No |
| `meta.page` | `number` | Página actual | ❌ No |
| `meta.count` | `number` | Cantidad de registros en esta página | ❌ No |

> [!warning] Deuda técnica: paginación perdida
> Los datos de paginación son descartados por el adapter. El `IApiResponse.meta` existe en el contrato (`{ pages, current, items }`) pero no se puebla. Ver [[deuda-tecnica]].

## Módulos que consumen esta entidad

- [[modulo-api]] (define la transformación)
- [[modulo-contracts]] (define el contrato de la entidad normalizada)
- [[api-comprador-by-razon-social]] (funcionalidad que la retorna)

## Error: forma del error del backend

Definido como `IErr` en `src/api/queries/comprador-by-razon-social.ts`:

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `success` | `boolean` | Indicador de éxito |
| `status` | `number` | Código HTTP |
| `data` | `string` | Mensaje de error en texto |

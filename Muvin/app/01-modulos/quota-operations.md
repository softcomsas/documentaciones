# Módulo: Quota Operations

## Descripción

Módulo contenedor de todas las operaciones sobre cupos. Agrupa los dominios de `assignment`, `quotas` y `requests`.

## Ruta

`/quota-operations` (i18n: valor en locale activo)

## Estructura

```
quota-operations/
├── container/container.ts       ← QuotaOperationsContainer
├── home/component.ts            ← QuotaOperationHomePage (dashboard/selector)
├── domains/
│   ├── assignment/              ← ACTIVO ✅
│   ├── quotas/                  ← PENDIENTE 🔴
│   └── requests/                ← PENDIENTE 🔴
└── routes.ts
```

## Menú

```typescript
menu: {
  name: 'Quota Operations',
  icon: 'tray',
}
```

## Dominios registrados

| Dominio | Ruta relativa | Estado |
|---|---|---|
| `assignment` | `assignment/` | ✅ Activo |
| `quotas` | `quotas/` | 🔴 Pendiente |
| `requests` | `requests/` | 🔴 Pendiente |

## Notas

- Las rutas de `quotas` y `requests` están comentadas en `routes.ts` — sus páginas (list, create, edit, view) están definidas en `PRIVATE_ROUTES` pero aún no implementadas.

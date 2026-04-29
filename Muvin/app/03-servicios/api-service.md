# ApiService — Servicio HTTP Central

## Descripción

Servicio singleton (`providedIn: 'root'`) que centraliza **todas las llamadas HTTP** de la aplicación usando un sistema de queries tipadas con lazy loading y caché integrado.

## Patrón de queries

En lugar de hacer `http.post(url, body)` directamente, cada endpoint está encapsulado en un archivo de query bajo `core/services/api/queries/`. El `ApiService` los resuelve dinámicamente:

```typescript
apiService.request('get-legacy-listado-zona', payload)
// → QUERIES_MAP.get('get-legacy-listado-zona')
// → import('./queries/get-legacy-listado-zona.ts')
// → query.value(payload)    → transforma payload al formato del servidor
// → HttpClient.post/get(url, body)
// → query.response(res)     → transforma respuesta al formato del cliente
// → query.err(err)          → maneja errores
```

## Sistema de caché

```typescript
interface CacheEntry<T> {
  readonly data: T;
  readonly timestamp: number;
  readonly ttl: number;
}
```
- Si `query.cache.save === true` → guarda en `Map<cacheKey, CacheEntry>`
- En siguiente llamada idéntica → retorna desde caché sin ir a red
- `cacheKey` = combinación de `queryKey + serialized(body)`

## Tipos del sistema

```typescript
// Tipo del payload del cliente
type TClient<K> = Parameters<TQueries[K]['value']>[0]

// Tipo del body enviado al servidor
type TServer<K> = ReturnType<TQueries[K]['value']>

// Tipo de la respuesta del servidor
type TRes<K> = Parameters<TQueries[K]['response']>[0]

// Tipo final retornado al componente
type TResult<K> = ReturnType<TQueries[K]['response']>
```

## Queries registradas

| Archivo | Descripción |
|---|---|
| `get-identity-list` | Lista de identidades/usuarios |
| `get-identity-one` | Detalle de una identidad |
| `get-legacy-buscar-para-asignar` | Buscar cupos/solicitudes para asignar |
| `get-legacy-cabecera` | Datos de cabecera de cupo |
| `get-legacy-centro-producto` | Centro-producto |
| `get-legacy-listado-zona` | Lista cupos y solicitudes por zona y fecha |
| `get-legacy-motivos-rechazo` | Motivos de rechazo disponibles |
| `get-legacy-zona-centro` | Zonas disponibles |
| `post-legacy-assign-auto` | Asignación automática |
| `post-legacy-assign-quotas` | Asignar cupos |
| `post-legacy-assign-requests` | Asignar solicitudes |
| `post-legacy-delete-cupos` | Eliminar cupos |
| `post-legacy-edit-application` | Editar aplicación |
| `post-legacy-rechazar-solicitudes` | Rechazar solicitudes |
| `put-legacy-cupo` | Actualizar cupo |

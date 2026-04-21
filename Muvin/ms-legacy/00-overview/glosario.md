# Glosario

> **Proyecto:** `muvin-ms-legacy`
> **Última revisión:** 2026-04-21

## Términos del dominio

### Comprador
Persona jurídica o física que realiza compras en el sistema Muvin. En el backend legacy, se representa con campos `id`, `razonSocial` y `cuitCuil`. Normalizado en este microservicio como `{ id, rs, cuit }`. Ver [[entidad-comprador]].

### CUIT / CUIL
**Código Único de Identificación Tributaria / Laboral.** Identificador fiscal argentino. Formato: `XX-XXXXXXXX-X` (con guiones). En el backend legacy se llama `cuitCuil`; en el contrato normalizado, `cuit`.

### Razón Social
Nombre legal de una empresa o persona jurídica bajo el que opera en registros comerciales y tributarios. Es el criterio principal de búsqueda del endpoint `comprador-by-razon-social`.

---

## Términos técnicos del microservicio

### Backend Legacy
Sistema REST externo de Muvin con el que este microservicio se comunica. URL base: `https://dev.muvinapp.com/api/backend/web/`. El término "legacy" se refiere a la antigüedad de ese sistema, no del microservicio proxy.

### CMD (`LEGACY_MICROSERVICE_CMD`)
Identificador de canal TCP de este microservicio. Valor por defecto: `CMD-LEGACY`. El microservicio consumidor debe enviar mensajes con `{ cmd: CMD }` para que NestJS los enrute a `AppController`.

### MessagePattern
Decorador de NestJS (`@MessagePattern`) que asocia un patrón de mensaje TCP con un método del controller. En este microservicio existe un único `@MessagePattern({ cmd: LEGACY_MICROSERVICE_CMD })`.

### Query Registry (`QUERIES_MAP`)
`Map<string, QueryDefinition>` que asocia claves de endpoint (ej. `"comprador-by-razon-social"`) con su definición completa (method, endpoint, adapters). Definido en `src/api/map.ts`.

### Adapter
Función pura que transforma datos entre formatos. En este microservicio hay tres adapters por query:
- **`payload`:** transforma los params de entrada antes de envirarlos al backend legacy.
- **`response`:** transforma la respuesta del backend legacy al formato del contrato.
- **`err`:** transforma un error del backend legacy al formato de error del contrato.

### `IApiResponse<T>`
Tipo genérico del contrato de respuesta: `{ code: number, data?: T, meta?: IMeta }`. Definido en `src/contracts/ms-legacy/api.ts`.

### `TContract<TResult, TParams>`
Tipo que encapsula el contrato de un endpoint (tipo de resultado + tipo de parámetros). Ver `src/types/request.ts`.

### Observable
Tipo de RxJS que representa un flujo asíncrono de valores. `AppService.request()` retorna un `Observable<IApiResponse<T>>`. El controller lo convierte a Promise con `firstValueFrom()`.

### IDENTITY
Función pasante que retorna su argumento sin transformación. Se usa en el adapter `payload` de queries GET donde no hay body.

### ValidationPipe
Pipe global de NestJS que valida y transforma el payload entrante. Configurado con `whitelist: true` (elimina campos no declarados) y `forbidNonWhitelisted: true` (lanza error si hay campos extra).

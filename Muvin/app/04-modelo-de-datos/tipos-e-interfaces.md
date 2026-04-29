# Modelo de Datos — Tipos e Interfaces

## TApi — Respuesta genérica del servidor

```typescript
// Respuesta exitosa
interface IApiSuccessResponseWithMeta<T> {
  readonly success: true;
  readonly data: T;
  readonly meta?: { readonly total: number };
}

// Respuesta fallida
interface IApiErrorResponse {
  readonly success: false;
  readonly error: {
    readonly code: number;
    readonly message: string | string[] | Record<string, string[]>;
  };
}

// Tipo discriminado (éxito | error)
type TApi<T = unknown> = IApiSuccessResponseWithMeta<T> | IApiErrorResponse;
```

---

## TData — Datos del dominio Assignment

```typescript
interface Segment {
  readonly product: IOption<number>;  // { value: number, label: string }
  readonly zone: IOption<number>;
}

interface Data {
  requests: (LegacyRequest & Segment)[];
  quotas: (LegacyQuota & Segment)[];
}

type TData = Readonly<Data>;
```

---

## TFacets — Opciones de filtro disponibles

```typescript
interface Facets {
  products: IOption<number>[];
  zones: IOption<number>[];
  destinations: IOption[];
  receivers: IOption[];
  commercialSenderSecondaryOnes: IOption[];
  brokers: IOption[];
  clients: IOption[];
  recipients: IOption[];
}
```

---

## TFiltered — Filtros activos

```typescript
interface Filtered {
  products: number[];
  zones: number[];
  destinations: string[];
  receivers: string[];
  commercialSenderSecondaryOnes: string[];
  brokers: string[];
  clients: string[];
  recipients: string[];
}
```

---

## TTerm — Agrupador de la grilla

```typescript
interface Term {
  key: 'product' | 'zone';
  label: string;
  value: number | null;
  resolver: (source: TSegmentSource) => { id: number; name: string };
}
```

---

## TViews — Modo de vista de las grillas

```typescript
type TView = 'INDIVIDUAL' | 'GROUPED';

interface Views {
  readonly quotas: TView;
  readonly requests: TView;
}
```

---

## IOption — Opción genérica de selector

```typescript
interface IOption<T = string> {
  value: T;
  label: string;
  disabled?: boolean;
}
```

---

## TStorage — Sesión del usuario (ConfigService)

```typescript
interface IConfiguration {
  readonly token: string;    // JWT access token
  readonly refresh: string;  // JWT refresh token
  readonly taxId: string;    // CUIT del usuario
}
```

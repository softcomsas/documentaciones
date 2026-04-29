# Glosario — muvin-app

| Término | Definición |
|---|---|
| **Cupo** | Autorización de una terminal para recibir un camión con determinado producto, en una fecha y volumen acordados |
| **Solicitud (Request)** | Pedido de un dador de carga o corredor para asignar un cupo a un camión específico |
| **Asignación** | Proceso de vincular un cupo disponible con una solicitud pendiente |
| **Terminal** | Planta de descarga donde se recibe el grano |
| **Zona** | Área geográfica que agrupa terminales o rutas de transporte |
| **Producto** | Tipo de grano (soja, maíz, trigo, etc.) |
| **Grilla semanal** | Vista del componente `PreviewComponent` que muestra cupos y solicitudes agrupados por producto/zona para los próximos 5 días |
| **TaxId** | CUIT del usuario autenticado, almacenado en `ConfigService` |
| **COE** | Código de Operación de Emergencia — identificador de descarga en tiempo real (RT) |
| **Facets** | Opciones de filtro disponibles calculadas a partir de los datos cargados (productos, zonas, destinos, etc.) |
| **Term** | Agrupador activo en la vista previa (`product` o `zone`) |
| **QUERIES_MAP** | Mapa de queries tipadas que usa `ApiService` para resolver endpoints de forma lazy |
| **Standalone Component** | Componente Angular 21 sin NgModule, con imports declarados directamente en el decorador |
| **Signal** | Primitiva reactiva de Angular (`signal()`) que reemplaza `BehaviorSubject` para estado local |
| **Prefix Private** | Prefijo dinámico de ruta privada obtenido desde `PREFIX_PRIVATE_ROUTES()` |
| **legacy** | Endpoints prefijados con `get-legacy-` o `post-legacy-` que consumen el API del sistema legado |

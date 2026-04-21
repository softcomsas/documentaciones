# Módulo Centros/Terminales

> **Última revisión:** 2026-04-21
> **Ruta:** `backend/controllers/Centro*.php`, `ConfiguracionCentroController.php`
> **Ver también:** [[modulo-cupos]], [[modulo-v3]], [[_indice-modulos]]

---

## Propósito

El dominio **Centros** gestiona las terminales/plantas de descarga de granos. Un centro es la entidad principal de destino para los cupos: representa una planta de silos, un puerto, una terminal de descarga, etc.

---

## Controladores

| Controlador | Propósito |
|-------------|-----------|
| `CentroController.php` | CRUD principal del centro/terminal |
| `CentroClienteController.php` | Clientes habilitados en un centro |
| `CentroCorredorController.php` | Corredores habilitados por centro |
| `CentroDestinatarioController.php` | Destinatarios habilitados por centro |
| `CentroEntregadorController.php` | Entregadores por centro |
| `CentroIntermediarioController.php` | Intermediarios por centro |
| `CentroOperadorController.php` | Operadores del centro |
| `CentroProductoController.php` | Productos que el centro acepta |
| `CentroTransporteController.php` | Transportistas habilitados por centro |
| `ConfiguracionCentroController.php` | Configuración detallada del centro |
| `ListaCentroController.php` | Listados y búsqueda de centros |
| `EvaluacionTerminalController.php` | Calificación/evaluación del terminal |
| `BocaController.php` | Bocas de descarga dentro del centro |
| `PlayaIntermediaController.php` | Playas intermedias de espera |

---

## Concepto de negocio

```
Centro (Terminal/Planta)
├── Configuración (horarios, capacidades, bocas)
├── Bocas de descarga
├── Playas intermedias
└── Habilitaciones (actores autorizados por el centro)
    ├── Clientes
    ├── Corredores
    ├── Destinatarios
    ├── Entregadores
    ├── Intermediarios
    ├── Operadores
    └── Transportistas
```

---

## Configuración de centro (ConfiguracionCentro)

La entidad `ConfiguracionCentro` contiene:
- Horarios de operación
- Capacidad máxima por día/turno
- Productos aceptados
- Tipos de acoplado aceptados
- Parámetros de turneada
- Flags de integración AFIP

---

## Relación con cupos

Cada cupo tiene un `id_centro` que lo vincula al centro de destino. El centro define las reglas de aceptación del cupo (productos, horarios, transportistas habilitados).

---

## Evaluación de terminales

El `EvaluacionTerminalController` permite que los operadores califiquen la experiencia en los terminales, generando métricas de servicio.

# Flujo: Creación de Pedido de Carga

> [[_indice-flujos]] | Módulo: [[modulo-cargas]]

## Descripción

Flujo desde que el usuario accede a Gestión de Cargas hasta confirmar la creación de un pedido de transporte vinculado a un cupo.

## Diagrama de flujo

```mermaid
sequenceDiagram
    participant Usuario
    participant GestionCargaPage
    participant CargaCuposDisponiblesPage
    participant CargaNuevoPedidoPage
    participant PedidoBloc
    participant MuvinProvider
    participant Backend

    Usuario->>GestionCargaPage: tap "Gestión Cargas" en menú
    GestionCargaPage-->>Usuario: Muestra cargas activas

    Usuario->>GestionCargaPage: tap "Cupos disponibles"
    GestionCargaPage->>CargaCuposDisponiblesPage: navegar
    CargaCuposDisponiblesPage->>PedidoBloc: getCuposDisponibles(userId, fecha)
    PedidoBloc->>MuvinProvider: GET v2/cupos/disponibles
    MuvinProvider->>Backend: request
    Backend-->>MuvinProvider: lista de cupos
    MuvinProvider-->>PedidoBloc: datos
    PedidoBloc-->>CargaCuposDisponiblesPage: stream emite lista
    CargaCuposDisponiblesPage-->>Usuario: Muestra cupos disponibles

    Usuario->>CargaCuposDisponiblesPage: tap cupo
    CargaCuposDisponiblesPage->>CargaNuevoPedidoPage: navegar con cupo

    Usuario->>CargaNuevoPedidoPage: completar form (patente, chofer, cantidad...)
    Usuario->>CargaNuevoPedidoPage: tap "Confirmar"

    CargaNuevoPedidoPage->>PedidoBloc: crearPedido(body)
    PedidoBloc->>MuvinProvider: POST pedido
    MuvinProvider->>Backend: request
    Backend-->>MuvinProvider: {pedidoId, estado}
    
    PedidoBloc->>MuvinProvider: PUT cupo/:id (asociar entregador)
    MuvinProvider->>Backend: request
    Backend-->>MuvinProvider: OK

    PedidoBloc-->>CargaNuevoPedidoPage: stream éxito
    CargaNuevoPedidoPage-->>Usuario: "Pedido creado"
    CargaNuevoPedidoPage->>GestionCargaPage: pop()
```

## Flujo alternativo: Pedido Rápido

```mermaid
sequenceDiagram
    participant Usuario
    participant GestionCargaPage
    participant PedidoBloc
    participant MuvinProvider

    Usuario->>GestionCargaPage: tap "Pedido Rápido"
    Usuario->>GestionCargaPage: completar form (sin cupo)
    GestionCargaPage->>PedidoBloc: pedidoRapido(body)
    PedidoBloc->>MuvinProvider: POST pedido/rapido
    MuvinProvider-->>PedidoBloc: {pedidoId}
    PedidoBloc-->>GestionCargaPage: éxito
```

## Riesgo: pedido huérfano

Si `POST pedido` tiene éxito pero `PUT cupo/:id` falla, existe un pedido sin cupo asociado. No hay rollback ni reintentos automáticos.

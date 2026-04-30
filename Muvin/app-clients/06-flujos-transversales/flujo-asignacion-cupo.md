# Flujo: Asignación de Cupo

> [[_indice-flujos]] | Módulo: [[modulo-cupos]]

## Descripción

Flujo completo desde que el Admin/Dador ingresa al módulo de Cupos hasta que confirma una solicitud de asignación.

## Diagrama de flujo

```mermaid
sequenceDiagram
    participant Admin
    participant CuposPage
    participant GestionPage
    participant SolicitudCupoBloc
    participant MuvinProvider
    participant SolicitudPage
    participant Backend

    Admin->>CuposPage: tap "Gestión Cupos" en menú
    CuposPage->>GestionPage: render
    GestionPage->>SolicitudCupoBloc: getCuposListado(fecha, cabecera, ccpp)
    SolicitudCupoBloc->>MuvinProvider: GET v3/cupo/listado
    MuvinProvider->>Backend: request
    Backend-->>MuvinProvider: [{id, producto, cantidad, disponibles...}]
    MuvinProvider-->>SolicitudCupoBloc: lista de cupos
    SolicitudCupoBloc-->>GestionPage: cuposStream emite datos
    GestionPage-->>Admin: Muestra lista de cupos
    
    Admin->>GestionPage: tap cupo
    GestionPage->>SolicitudPage: navegar con cupo preseleccionado
    Admin->>SolicitudPage: completar formulario (demandante, cantidad, tipo)
    Admin->>SolicitudPage: tap "Confirmar"
    
    alt Solicitud simple
        SolicitudPage->>SolicitudCupoBloc: cargaSolicitud(body)
        SolicitudCupoBloc->>MuvinProvider: POST v3/cupo/carga-solicitud
    else Solicitud distribuida
        SolicitudPage->>SolicitudCupoBloc: cargaSolicitudDistribuida(body)
        SolicitudCupoBloc->>MuvinProvider: POST v3/cupo/carga-solicitud-distribuida
    end
    
    MuvinProvider->>Backend: request
    Backend-->>MuvinProvider: {solicitudId, estado: "pendiente"}
    MuvinProvider-->>SolicitudCupoBloc: resultado
    SolicitudCupoBloc-->>SolicitudPage: operacionStream emite éxito
    SolicitudPage-->>Admin: Snackbar "Solicitud creada"
    SolicitudPage->>GestionPage: pop() (volver)
```

## Variantes del flujo

| Variante | Trigger | Endpoint |
|----------|---------|----------|
| Solicitud simple | 1 demandante, 1 cantidad | `POST v3/cupo/carga-solicitud` |
| Solicitud distribuida | múltiples demandantes | `POST v3/cupo/carga-solicitud-distribuida` |
| Asignación directa | Admin ya conoce al demandante | `POST v3/cupo/asignar` / `asignar2` |
| Recuperar cupo | Revertir asignación previa | `POST v3/cupo/recuperar` |

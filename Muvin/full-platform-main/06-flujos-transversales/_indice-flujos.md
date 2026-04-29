# Índice de Flujos Transversales

> **Última revisión:** 2026-04-29

| # | Flujo | Descripción | Módulos involucrados | Archivo |
|---|---|---|---|---|
| F-01 | Autenticación y acceso a MFE | Login → token → carga de MFE en shell | auth-app, main, shared | [[flujo-autenticacion]] |
| F-02 | Carga de Cupo con validación AFIP | Usuario crea cupo → validación contra AFIP → persistencia | descargas-app (FE + BE) + AFIP | [[flujo-carga-cupo-afip]] |
| F-03 | Generación de Carta Porte | Completar formulario → generar CTG → carta porte PDF | descargas-app | [[flujo-carta-porte]] |
| F-04 | Flujo de Descarga completo | Cupo aprobado → descarga → valores de calidad → cierre | descargas-app | [[flujo-descarga-completa]] |
| F-05 | Carga de Documento con aprobación | Entidad carga doc → revisión → aprobación/rechazo | documentacion-app | [[flujo-carga-documento]] |
| F-06 | Ciclo de vida de una Oferta | Creación → publicación → cierre/expiración | oferta-app | [[flujo-ciclo-oferta]] |
| F-07 | Proceso de Transferencia de Cupo | Origen → transferencia → destino confirma | descargas-app | [[flujo-transferencia-cupo]] |

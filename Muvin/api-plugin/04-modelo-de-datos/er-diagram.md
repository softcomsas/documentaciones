# Diagrama ER — Modelo de Datos

## Tablas principales

```
┌──────────────────┐       ┌────────────────────────────┐
│     cliente      │       │   cliente_configuracion    │
├──────────────────┤       ├────────────────────────────┤
│ id (PK)          │──┐    │ id (PK)                    │
│ nombre           │  │    │ usermail                   │
│ cuit             │  ├───►│ pwdmail                    │
│ user_muvin       │  │    │ srvmail                    │
│ password (enc)   │  │    │ activo                     │
│ activo           │  │    │ cliente_id (FK)            │
└──────────────────┘  │    │ proceso_id (FK) ─────────┐ │
                      │    └────────────────────────────┘ │
                      │                                   │
                      │    ┌──────────────────┐           │
                      │    │     proceso      │           │
                      │    ├──────────────────┤           │
                      └───►│ id (PK)          │◄──────────┘
                           │ nombre           │
                           │ activo           │
                           │ metodo (callable)│
                           │ cliente_id (FK)  │
                           └──────────────────┘


┌─────────────────────────────────────────────┐
│               cupo_documento                │
├─────────────────────────────────────────────┤
│ id (PK)                                     │
│ nombre_archivo                              │
│ titular                                     │
│ rem_com_productor                           │
│ rem_com_ventaprimaria                       │
│ destinatario                                │
│ destino                                     │
│ grano_especie                               │
│ cosecha                                     │
│ estado  [PENDIENTE | PROCESADO | ERROR]     │
│ cliente_id (FK → cliente)                   │
│ created_at                                  │
│ updated_at                                  │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│                descargas_rt                 │
├─────────────────────────────────────────────┤
│ id (PK)                                     │
│ coe                                         │
│ grano                                       │
│ cosecha                                     │
│ cuitDepositario                             │
│ nroPlanta                                   │
│ cuitDepositante                             │
│ kilos                                       │
│ estado  [PENDIENTE | PROCESADO | ERROR]     │
│ created_at                                  │
└─────────────────────────────────────────────┘

┌──────────────────────┐
│     cupo_codigo      │
├──────────────────────┤
│ id (PK)              │
│ codigo (alfa-num)    │
│ cupo_documento_id    │  ─────► cupo_documento.id
└──────────────────────┘

┌──────────────────────────────────┐
│     oncca_destino_producto       │
├──────────────────────────────────┤
│ id (PK)                          │
│ destino_id                       │
│ producto_id                      │
│ (campos ONCCA)                   │
└──────────────────────────────────┘

┌──────────────┐     ┌──────────────┐     ┌───────────────────┐
│   productos  │     │   destinos   │     │   cuit_empresas   │
├──────────────┤     ├──────────────┤     ├───────────────────┤
│ id           │     │ id           │     │ id                │
│ nombre       │     │ nombre       │     │ cuit              │
│ codigo       │     │ codigo       │     │ nombre            │
└──────────────┘     │ empresa      │     └───────────────────┘
                     └──────────────┘
```

## Tablas de notificaciones

```
┌──────────────────────────────────────────┐
│          cupo_mail_notif                 │
├──────────────────────────────────────────┤
│ id (PK)                                  │
│ cupo_documento_id (FK)                   │
│ email_destino                            │
│ estado                                   │
│ created_at                               │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│          email_template                  │
├──────────────────────────────────────────┤
│ id (PK)                                  │
│ nombre                                   │
│ asunto                                   │
│ cuerpo (HTML)                            │
│ tipo                                     │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│       descargas_rt_notif                 │
├──────────────────────────────────────────┤
│ id (PK)                                  │
│ descargas_rt_id (FK)                     │
│ estado                                   │
│ created_at                               │
└──────────────────────────────────────────┘
```

## Estados de cupo_documento

| Estado | Descripción |
|---|---|
| `PENDIENTE` | Cupo parseado, pendiente de enviar a Muvin API |
| `PROCESADO` | Cupo cargado exitosamente en Muvin |
| `ERROR` | Falló el parseo o la carga en Muvin |

## Estados de descargas_rt

| Estado | Descripción |
|---|---|
| `PENDIENTE` | DescargaRT recibida, pendiente de procesar |
| `PROCESADO` | DescargaRT procesada exitosamente |
| `ERROR` | Error en el procesamiento |

## Evolución del esquema

El proyecto tiene más de 70 migraciones desde 2021. Los hitos principales:

| Fecha | Cambio |
|---|---|
| 2021-10 | Tablas iniciales: `mail`, `cupo_documento`, notificaciones |
| 2022-04 | Tabla `oncca_destino_producto` |
| 2022-07 | Refactorización completa de tabla `proceso` |
| 2022-07 | Tabla `cuit_empresas`, `destinos_dreyfus` |
| 2022-08 | Credenciales en `cliente` (`alter_cliente_add_credenciales`) |
| 2025-01 | Tabla `descargas_rt` (PluginV2) |
| 2025-03 | Tabla `descargas_rt_notif` |

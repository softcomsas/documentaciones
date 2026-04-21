# Diagrama ER Global — Modelo de Datos

> **Última revisión:** 2026-04-21
> **Ver también:** [[_indice-entidades]], [[modulo-cupos]], [[modulo-viajes]], [[modulo-choferes]]

---

## Entidades core y sus relaciones

```mermaid
erDiagram
    user {
        int id PK
        string username
        string password_hash
        string email
        string auth_key
        int status
        int created_at
        int updated_at
    }

    access_tokens {
        int id PK
        string token
        int expires_at
        string auth_code
        int user_id FK
        string app_id
        int rol_id
    }

    persona_rol {
        int id PK
        int id_rol FK
        int id_usuario FK
        int activo
    }

    configuracion_centro {
        int id PK
        int id_centro FK
        int capacidad_diaria
        string horario_inicio
        string horario_fin
    }

    cupo {
        int id PK
        string idCupoTerminal
        string fecha
        int id_producto FK
        int cosecha
        string estado
        string cartaPorte
        int ctg
        int idCupoEstado
        int id_entregador FK
        int id_destino FK
        int idTerminal FK
        string dominio
        string cuitChoferAfip
        string cuitTransportistaAfip
        string fechaActivado
        string fechaArribado
        string fechaTomado
        string fechaConfirmado
        string fechaAnulado
        string fechaDescargado
    }

    carta_porte {
        int id PK
        int idTipoCartaPorte
        string idEstadoCartaPorte
        string fechaEmisionCartaPorte
        string fechaVencimiento
        string nombreChofer
        int idCuitChofer
        string nombreTransportista
        int idCuitTransportista
        string nombreDestinatario
        int idCuitDestinatario
        string granoNombre
        int cosecha
        float granoPesoNeto
        string nroContrato
    }

    viaje {
        int id PK
        int id_pedido FK
        int id_chofer_equipo FK
        string fecha
        int id_cupo FK
        string carta_porte
        int id_destino FK
        int id_corredor FK
        int id_destinatario FK
        int id_estado
        float distance
        int seguro
        int bloqueado
        string observaciones
    }

    pedido {
        int id PK
        int id_centro FK
        int id_producto FK
        int cosecha
        string fecha
        int id_estado
    }

    chofer_equipo {
        int id PK
        int id_chofer FK
        int id_camion FK
        int id_acoplado FK
        int activo
    }

    camion {
        int id PK
        string dominio
        int id_transportista FK
        string marca
        string modelo
    }

    acoplado {
        int id PK
        string dominio
        int id_transportista FK
        int id_tipo_acoplado FK
    }

    producto {
        int id PK
        string nombre
        int cod_grano
    }

    cabecera {
        int id PK
        int id_centro FK
        int id_producto FK
        int cosecha
        string fecha_desde
        string fecha_hasta
    }

    calada_rechazada {
        int id PK
        int id_viaje FK
        string motivo
        string fecha
    }

    auditoria {
        int id PK
        int id_usuario FK
        string accion
        string modelo
        int id_modelo
        string valores_anteriores
        string valores_nuevos
        string fecha
    }

    user ||--o{ access_tokens : "tiene"
    user ||--o{ persona_rol : "tiene_roles"
    persona_rol ||--o| configuracion_centro : "config (si es centro)"
    persona_rol ||--o{ cupo : "es_terminal_de"
    persona_rol ||--o{ viaje : "como_corredor"
    cupo ||--o{ viaje : "tiene_viajes"
    cupo ||--o| carta_porte : "tiene_cpe"
    viaje ||--o{ calada_rechazada : "puede_tener"
    viaje }|--|| chofer_equipo : "asignado_a"
    viaje }|--|| pedido : "cubre"
    chofer_equipo }|--|| camion : "usa"
    chofer_equipo }|--|| acoplado : "arrastra"
    cupo }|--|| producto : "de_producto"
    pedido }|--|| producto : "para_producto"
    cabecera }|--|| persona_rol : "del_centro"
    cabecera }|--|| producto : "para_producto"
```

---

## Notas del modelo

### Polimorfismo en PersonaRol

`PersonaRol` actúa como tabla polimórfica: un mismo registro puede ser:
- Centro (`id_rol = 3`)
- Transportista (`id_rol = 4`)
- Corredor (`id_rol = 9`)
- Etc.

Los modelos específicos heredan de `PersonaRol`:
```
PersonaRol
├── Centro (ROL_CENTRO = 3)
└── (otros roles usan PersonaRol directamente)
```

### Convención de nombres mixta

El modelo tiene inconsistencia histórica en nombres de columnas:
- Algunas usan `camelCase`: `idCupoTerminal`, `idCupoEstado`
- Otras usan `snake_case`: `id_producto`, `id_destino`

Esto refleja el crecimiento orgánico del sistema a lo largo de años.

---

## Tablas de catálogo

| Tabla (modelo) | Propósito |
|---------------|-----------|
| `Producto` | Catálogo de granos y productos |
| `TipoProducto` | Tipos de producto |
| `ProductoCondicion` | Condiciones de calidad por producto |
| `Provincias` | Catálogo de provincias |
| `AfipLocalidades` | Localidades habilitadas AFIP |
| `TipoDespacho` | Tipos de despacho |
| `TipoInterviniente` | Tipos de actores en carta de porte |
| `MotivoAnularCupo` | Motivos de anulación |
| `MotivoAnularSolicitud` | Motivos de anulación de solicitudes |
| `MotivoRechazoDemandaCupo` | Motivos de rechazo de demanda |
| `MotivoRecuperarCupo` | Motivos de recuperación |
| `NotificacionesTipo` | Tipos de notificación |
| `MenuesByRol` | Menús por rol |
| `Modulos` | Catálogo de módulos |

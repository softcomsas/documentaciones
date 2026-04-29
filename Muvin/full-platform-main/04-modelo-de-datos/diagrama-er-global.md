# Diagrama ER Global

> **Última revisión:** 2026-04-29
> **Nota:** Diagrama construido a partir de los modelos Sequelize de `descargas-app` y la inferencia de controladores Yii2. Requiere validación contra la base de datos real.

```mermaid
erDiagram
    CUPO {
        int id PK
        string estado
        string terminal
        string grano
        int volumen
        date fecha
        string ctg
    }

    ADENDA {
        int id PK
        int cupo_id FK
        string descripcion
        date fecha
        string archivo_path
    }

    ADENDA_ARCHIVO {
        int id PK
        int adenda_id FK
        string nombre_archivo
        string path
    }

    CARTA_PORTE_TREN {
        int id PK
        string numero_ctg
        string estado
        date fecha_emision
    }

    CARTA_PORTE_TREN_ARCHIVO {
        int id PK
        int carta_porte_id FK
        string archivo_path
    }

    TRANSFERENCIA {
        int id PK
        int cupo_origen_id FK
        string terminal_destino
        int volumen
        date fecha
    }

    TRANSFERENCIA_ARCHIVO {
        int id PK
        int transferencia_id FK
        string archivo_path
    }

    MOTIVO_ERROR {
        int id PK
        string descripcion
        string codigo
    }

    VALORES_CALIDAD {
        int id PK
        int cupo_id FK
        float humedad
        float proteina
        float gluten
    }

    CRON {
        int id PK
        string nombre
        datetime ultima_ejecucion
        string estado
    }

    VIAJE {
        int id PK
        int chofer_id FK
        int equipo_id FK
        int origen_id FK
        int destino_id FK
        string estado
        date fecha
    }

    CARGA {
        int id PK
        int viaje_id FK
        int producto_id FK
        float volumen
        string estado
    }

    CHOFER {
        int id PK
        string nombre
        string dni
        string licencia
    }

    EQUIPO {
        int id PK
        string patente
        string tipo
        int intermediario_id FK
    }

    OFERTA {
        int id PK
        string producto
        float precio
        int volumen
        date fecha_vigencia
        string estado
    }

    DOCUMENTO {
        int id PK
        int entidad_id FK
        int categoria_id FK
        string nombre
        date fecha_vencimiento
        string estado_presentacion
    }

    CATEGORIA {
        int id PK
        string nombre
        string tipo
    }

    ENTIDAD {
        int id PK
        string nombre
        string cuit
        string tipo
    }

    CUPO ||--o{ ADENDA : "tiene"
    ADENDA ||--o{ ADENDA_ARCHIVO : "tiene"
    CUPO ||--o{ VALORES_CALIDAD : "tiene"
    CUPO ||--o| TRANSFERENCIA : "origen de"
    CARTA_PORTE_TREN ||--o{ CARTA_PORTE_TREN_ARCHIVO : "tiene"
    TRANSFERENCIA ||--o{ TRANSFERENCIA_ARCHIVO : "tiene"

    VIAJE ||--o{ CARGA : "contiene"
    VIAJE }o--|| CHOFER : "conducido por"
    VIAJE }o--|| EQUIPO : "usa"
    EQUIPO }o--o| INTERMEDIARIO : "pertenece a"

    DOCUMENTO }o--|| ENTIDAD : "presentado por"
    DOCUMENTO }o--|| CATEGORIA : "clasificado en"
```

> ⚠️ Este diagrama es una aproximación basada en los modelos disponibles. Las relaciones exactas (claves foráneas, índices, nulabilidad) deben verificarse contra el esquema real de la DB o las migraciones.

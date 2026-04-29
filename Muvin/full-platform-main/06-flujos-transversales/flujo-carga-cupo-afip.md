# Flujo: Carga de Cupo con Validación AFIP

> **Módulos involucrados:** [[modulo-descargas]], AFIP (externo)
> **Tipo:** Proceso de integración

## Diagrama de Flujo

```mermaid
flowchart TD
    A([Inicio: Usuario accede a Descargas]) --> B[Selecciona terminal y período]
    B --> C[Completa formulario de cupo\nCupoForm]
    C --> D{Validación de campos\nclass-validator + NoHtmlPipe}
    D -->|Error de validación| E[Muestra errores al usuario]
    E --> C
    D -->|OK| F[POST /descargas\nEnvía CuposPayload al backend]
    F --> G{Backend guarda cupo}
    G -->|Error| H[Retorna error 422\ncon mensaje]
    H --> E
    G -->|OK| I[Cupo guardado en DB]
    I --> J{Usuario requiere\nvalidar con AFIP?}
    J -->|No| K([Fin: Cupo registrado])
    J -->|Sí| L[POST /descargas/actualizar-cupo-afip\nEnvía IDs de cupos]
    L --> M{AFIP disponible?}
    M -->|No| N[Error: AFIP no disponible\nMensaje al usuario]
    N --> K
    M -->|Sí| O[AFIP retorna datos actualizados\nDiferencias detectadas]
    O --> P[Backend actualiza cupos\ncon datos de AFIP]
    P --> Q[Retorna diferencias al frontend]
    Q --> R[Frontend muestra resumen\nde cambios]
    R --> K
```

## Servicios invocados

| Paso | Verbo | Endpoint | Payload |
|---|---|---|---|
| Guardar cupo | POST | `/descargas` | `CuposPayload` |
| Validar con AFIP | POST | `/descargas/actualizar-cupo-afip` | `{ cupos: [id1, id2, ...] }` |

## Entidades afectadas

- **Escribe:** [[entidad-cupo]]
- **Lee:** API externa AFIP

## Riesgos

- 🔴 Si AFIP no responde, el mensaje de error es genérico y no orientativo para el usuario.
- ⚠️ No se documenta un mecanismo de retry automático en caso de fallo de AFIP.

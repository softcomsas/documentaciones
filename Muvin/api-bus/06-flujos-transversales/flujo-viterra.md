# Flujo: Integración Viterra (Base de Datos Local)

> **Aplica a:** módulo `viterra`
> **Última revisión:** 2026-04-29

---

## Descripción

A diferencia del resto de los módulos, **Viterra no realiza llamadas HTTP a un sistema externo**. En su lugar, api-bus expone vía REST los datos de una base de datos relacional que el sistema Viterra sincroniza y gestiona.

Este patrón es más simple: JWT → validación → consulta ActiveRecord → normalización.

---

## Diagrama de secuencia

```mermaid
sequenceDiagram
    autonumber
    participant APP as App/Panel
    participant MW as JWT Middleware
    participant CTRL as DestinoController
    participant FORM as SituacionTurnos Form
    participant AR as HorarioPuerto\nActiveRecord
    participant DB as Base de Datos\nViterra

    APP->>MW: GET /viterra/destino/situacion-puerto?fecha=...&producto=... (Bearer JWT)
    MW->>MW: Validar JWT
    MW->>CTRL: request validado

    CTRL->>FORM: new SituacionTurnos(params)
    FORM->>FORM: validate() — reglas Yii
    alt Validación fallida
        FORM-->>CTRL: errores
        CTRL-->>APP: {success: false, status: 422, data: errores}
    else Validación exitosa
        FORM-->>CTRL: datos validados
        CTRL->>AR: HorarioPuerto::find()->where([...])
        AR->>DB: SELECT horario_puerto JOIN ...
        DB-->>AR: rows
        AR-->>CTRL: array de modelos
        CTRL->>CTRL: beforeSend — normalizeResponse
        CTRL-->>APP: {success: true, status: 200, data: [...horarios]}
    end
```

---

## Flujo informaciónVentanilla

```mermaid
flowchart LR
    A[GET /viterra/destino/\ninformacion-ventanilla/123] --> B[DestinoController\ninformacionVentanilla]
    B --> C[HorarioPuerto::findOne\nid_horario = 123]
    C --> D[(DB Viterra)]
    D --> C
    C -->|modelo con relaciones| B
    B -->|{success, status, data}| A
```

---

## Consideraciones de diseño

| Aspecto | Descripción |
|---------|-------------|
| **Sin HTTP externo** | Toda la data viene de DB local; latencia muy baja |
| **Read-only** | api-bus nunca escribe en el esquema Viterra |
| **Sincronización** | Es responsabilidad del sistema Viterra mantener la DB actualizada |
| **Conexión DB** | Configurada en `config/main.php` como conexión Yii 2 (`db`) |
| **12 modelos AR** | Representan el esquema completo de Viterra visible a api-bus |

---

## Posibles puntos de falla

| Fallo | Impacto | Mitigación |
|-------|---------|------------|
| DB Viterra no accesible | 🔴 Módulo completo caído | Monitoreo de conectividad DB |
| Datos desactualizados | 🟡 Info incorrecta a usuario | Verificar frecuencia de sync Viterra |
| Schema drift (tablas cambian) | 🔴 Errores ActiveRecord | Versionar schema, tests de integración |

---

## Referencias

- [[modulo-viterra]]
- [[viterra-endpoints]]
- [[entidades-viterra]]

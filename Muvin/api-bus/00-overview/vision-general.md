# Visión General — api-bus

## ¿Qué es?

`api-bus` es un **bus de integración** construido sobre Yii 2 (PHP). Su responsabilidad es actuar como proxy seguro y unificado entre los servicios internos de Muvin y los sistemas externos de terceros. Centraliza:

- La **autenticación** contra cada sistema externo (OAuth2, sesión, API key)
- El **cifrado/descifrado AES-128-ECB** de credenciales sensibles en tránsito
- La **comunicación HTTP** via cURL hacia los APIs externos
- La **normalización de respuestas** al formato estándar Muvin `{success, status, data}`

## A quién sirve

| Consumidor | Qué necesita |
|---|---|
| `ms-legacy` / `full-platform` | Consultar cupos y contratos en SiloHub |
| `descargas-app` / `api-plugin` | Actualizar CTG en Puerto Timbúes |
| Frontend de destino | Ver turnos y ventanillas de Viterra |
| Internos Muvin | Ubicar camiones vía CerealTrack (Monsanto/Bayer) |
| Agroquímicos | Ver pedidos Zarcam + ubicación GPS San Miguel |

## Estado actual

| Aspecto | Estado |
|---|---|
| Módulo `silohub` | ✅ Activo en producción |
| Módulo `timbues` | ✅ Activo en producción |
| Módulo `viterra` | ✅ Activo (con modelos ActiveRecord locales) |
| Módulo `monsanto` | ✅ Activo |
| Módulo `agroquimicos` | ✅ Activo |
| Tests automatizados | 🔴 Sin tests funcionales detectados |
| Documentación previa | 🔴 Solo README template de Yii 2 |

## Números relevantes

| Métrica | Valor |
|---|---|
| Módulos de integración | 5 (+ 1 común) |
| Controladores | 5 |
| Sistemas externos integrados | 6 |
| Tabla en BD propia | 1 (`agroq_san_miguel_key`) |
| Migraciones | 1 |

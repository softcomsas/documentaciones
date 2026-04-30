# Deuda Técnica — app-clients

> [[README]] · [[hotspots]] · [[recomendaciones-modernizacion]]

## Inventario de deuda técnica

| ID | Área | Descripción | Severidad | Esfuerzo |
|----|------|-------------|-----------|---------|
| DT-01 | Seguridad | Credenciales hardcodeadas en `config.dart` | 🔴 Crítico | Bajo |
| DT-02 | Seguridad | FCM server token en cliente | 🔴 Crítico | Bajo |
| DT-03 | Seguridad | Captcha hardcodeado en login | 🔴 Crítico | Bajo |
| DT-04 | Seguridad | `access_token` sin cifrado en SharedPreferences | 🔴 Alto | Bajo |
| DT-05 | SDK | Flutter/Dart pre-null-safety (2.x) | 🔴 Alto | Muy Alto |
| DT-06 | Dependencias | `rxdart ^0.23.x` legacy | 🔴 Alto | Alto |
| DT-07 | Dependencias | Firebase packages pre-FlutterFire unificado | 🔴 Alto | Alto |
| DT-08 | Dependencias | `socket_io_client ^0.9.x` legacy | 🔴 Alto | Alto |
| DT-09 | Auth | Token refresh no implementado | 🟠 Alto | Medio |
| DT-10 | Auth | Sin invalidación de token en logout | 🟠 Alto | Bajo |
| DT-11 | Arquitectura | `MuvinProvider` monolítico (~515 líneas) | 🟠 Medio | Medio |
| DT-12 | Arquitectura | Sin capa Repository (BLoC → Provider directo) | 🟠 Medio | Alto |
| DT-13 | Arquitectura | Sin inyección de dependencias formal | ⚠️ Medio | Alto |
| DT-14 | Cargas | Falta transaccionalidad POST pedido + PUT cupo | 🟠 Alto | Alto |
| DT-15 | Datos | Campos de fecha como `String` sin parseo | ⚠️ Bajo | Bajo |
| DT-16 | Datos | `estado` de solicitud como string libre | ⚠️ Bajo | Bajo |
| DT-17 | Operaciones | Sin environments (dev/staging/prod) | ⚠️ Medio | Medio |
| DT-18 | Operaciones | Sin CI/CD documentado | ⚠️ Bajo | Medio |
| DT-19 | Calidad | Sin cobertura de tests automatizados visible | ⚠️ Medio | Alto |
| DT-20 | WebSocket | Sin reconexión automática con backoff | ⚠️ Medio | Bajo |

## Resumen por severidad

| Severidad | Cantidad |
|-----------|---------|
| 🔴 Crítico | 4 |
| 🔴 Alto | 4 |
| 🟠 Alto | 4 |
| ⚠️ Medio | 5 |
| ⚠️ Bajo | 3 |

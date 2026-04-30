# Deuda Técnica

> [[hotspots]] | [[recomendaciones-modernizacion]]

## Categorías

### DT-SEG — Seguridad

| ID | Descripción | Impacto | Esfuerzo |
|----|-------------|---------|----------|
| DT-SEG-01 | Token generado con `md5(uniqid())` (entropía baja) | 🔴 Crítico | Bajo |
| DT-SEG-02 | `cookieValidationKey` hardcodeada en repositorio | 🔴 Crítico | Bajo |
| DT-SEG-03 | Passphrase del certificado AFIP en texto plano en BD | 🔴 Crítico | Medio |
| DT-SEG-04 | Certificado privado X.509 en texto plano en BD | 🔴 Crítico | Alto |
| DT-SEG-05 | CSRF deshabilitado globalmente (aceptable para REST puro, documentar explícitamente) | 🟡 Bajo | Bajo |
| DT-SEG-06 | Sin rate limiting en `/login/login` (fuerza bruta) | 🔴 Alto | Medio |

### DT-FIAB — Fiabilidad

| ID | Descripción | Impacto | Esfuerzo |
|----|-------------|---------|----------|
| DT-FIAB-01 | Caché sin TTL: resultados AFIP nunca expiran | 🔴 Alto | Bajo |
| DT-FIAB-02 | Condición de carrera al escribir `cert.key` / `key.pem` | 🔴 Alto | Medio |
| DT-FIAB-03 | Sin rollback si `file_put_contents` falla tras INSERT en BD | 🟠 Medio | Medio |
| DT-FIAB-04 | Sin timeout en `SoapClient` AFIP → proceso PHP bloqueado | 🟠 Medio | Bajo |
| DT-FIAB-05 | Sin manejo de TA.xml corrompido o truncado | 🟠 Medio | Bajo |

### DT-MANT — Mantenibilidad

| ID | Descripción | Impacto | Esfuerzo |
|----|-------------|---------|----------|
| DT-MANT-01 | PHP 7.4 EOL (nov-2022) | 🟠 Medio | Alto |
| DT-MANT-02 | Yii2 en modo mantenimiento (sin features nuevas) | 🟡 Bajo | Alto |
| DT-MANT-03 | `id_user = 1` hardcodeado → imposible multi-tenancy | 🟠 Medio | Alto |
| DT-MANT-04 | Swagger 2.0 (no OpenAPI 3) via `light/yii2-swagger` | 🟡 Bajo | Medio |
| DT-MANT-05 | Sin tests automatizados (ni unitarios ni de integración) | 🟠 Medio | Alto |
| DT-MANT-06 | Errores AFIP devueltos con HTTP 200 | 🟠 Medio | Bajo |
| DT-MANT-07 | Lógica de negocio mezclada en ActiveRecord | 🟡 Bajo | Alto |

## Prioridad de resolución

```
Inmediato (< 1 semana):
  DT-SEG-01, DT-SEG-02, DT-FIAB-01, DT-FIAB-04, DT-MANT-06

Corto plazo (< 1 mes):
  DT-SEG-03, DT-SEG-06, DT-FIAB-02, DT-FIAB-03, DT-FIAB-05

Largo plazo (roadmap):
  DT-SEG-04, DT-MANT-01, DT-MANT-02, DT-MANT-03, DT-MANT-05, DT-MANT-07
```

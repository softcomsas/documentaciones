# Deuda Técnica — api-bus

> **Última revisión:** 2026-04-29
> **Ver también:** [[security-inventory]] para hallazgos de seguridad específicos.

---

## Resumen ejecutivo

api-bus presenta una deuda técnica **alta** en múltiples dimensiones: tecnología obsoleta, prácticas de seguridad inseguras, ausencia de tests, y código heredado sin refactorizar.

---

## Clasificación de deuda

### 🔴 Crítico — Bloquea modernización segura

| ID | Ítem | Impacto |
|----|------|---------|
| DT-01 | PHP 5.6 (EOL dic 2018) | Sin parches de seguridad, incompatible con librerías modernas |
| DT-02 | AES-128-ECB para cifrado de parámetros | Cifrado inseguro; ECB no oculta patrones |
| DT-03 | Credenciales hardcodeadas en `main.php` | Exposición de secretos en repositorio |
| DT-04 | SSL verification deshabilitada (`verifyPeer=false`) | Vulnerable a MITM en todas las integraciones |
| DT-05 | Sin tests automatizados | Imposible garantizar comportamiento al modificar |

### 🟡 Alto — Dificulta mantenimiento

| ID | Ítem | Impacto |
|----|------|---------|
| DT-06 | Yii 2 (soporte hasta dic 2024) | Framework llegando a EOL |
| DT-07 | PHP sin typing estricto | Errores difíciles de detectar sin runtime |
| DT-08 | Sin inyección de dependencias real | Acoplamiento alto entre controladores y componentes |
| DT-09 | `LoginSiloHub` singleton estático | Difícil de testear, estado global |
| DT-10 | `SanMiguelKey` sin TTL ni historial | Token GPS puede expirar sin detección |
| DT-11 | `comandos de ejemplo` no eliminados | `HelloController.php`, `ContactForm.php`, `LoginForm.php` innecesarios |
| DT-12 | URL `desa3.securityconsultant.com.ar` en producción | Posible entorno incorrecto |
| DT-13 | `empresaSiloHub = 'PRU'` | Valor de prueba posiblemente en producción |

### 🟢 Bajo — Mejoras de calidad

| ID | Ítem | Impacto |
|----|------|---------|
| DT-14 | Sin documentación inline (PHPDoc) | Onboarding lento |
| DT-15 | README.md es template Yii sin contenido real | No aporta valor |
| DT-16 | `rutas.php` vacío | Posible intención no implementada |
| DT-17 | Sin healthcheck endpoint | Dificulta monitoreo y orquestación |
| DT-18 | Sin manejo centralizado de errores de integración | Cada módulo maneja (o no) sus propios errores |

---

## Hotspots de código

Archivos con mayor concentración de deuda:

| Archivo | Problemas | Prioridad |
|---------|-----------|-----------|
| `config/main.php` | DT-03 (credenciales), DT-13 (PRU) | 🔴 |
| `common/components/BaseCurl.php` | DT-04 (SSL off) | 🔴 |
| `silohub/components/SiloHub.php` | DT-02 (AES-ECB), DT-09 (singleton) | 🔴 |
| `agroquimicos/models/SanMiguelKey.php` | DT-10 (sin TTL) | 🟡 |
| `modules/*/controllers/*.php` | DT-08 (acoplamiento), DT-07 (no typing) | 🟡 |

---

## Plan de modernización sugerido

### Fase 1 — Seguridad crítica (inmediata)

1. **Migrar credenciales** a variables de entorno (`.env` + `vlucas/phpdotenv`)
2. **Habilitar SSL verification** en `BaseCurl` (reparar certificados si necesario)
3. **Reemplazar AES-ECB** por AES-256-GCM o TLS mutuo
4. **Rotar todos los secretos** expuestos en el repositorio

### Fase 2 — Actualización de stack (corto plazo)

1. **Actualizar PHP** de 5.6 → 8.2 (verificar compatibilidad Yii 2)
2. **Actualizar Yii 2** a la última versión soportada o evaluar migración a Yii 3 / Laravel
3. **Agregar typing estricto** (`declare(strict_types=1)`) en todos los archivos PHP

### Fase 3 — Calidad y mantenibilidad (mediano plazo)

1. **Agregar tests** con PHPUnit (unitarios para componentes, integración con mocks HTTP)
2. **Refactorizar singletons** a inyección de dependencias (Yii DI container)
3. **Agregar TTL y rotación** a `SanMiguelKey`
4. **Endpoint `/health`** para monitoreo
5. **Eliminar archivos muertos** (HelloController, ContactForm, LoginForm web)

---

## Referencias

- [[security-inventory]]
- [[stack-tecnologico]]
- [[build-y-despliegue]]

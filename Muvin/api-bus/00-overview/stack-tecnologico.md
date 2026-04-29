# Stack Tecnológico — api-bus

| Tecnología | Versión | Propósito | Estado vendor | Riesgo |
|---|---|---|---|---|
| PHP | ≥ 5.6.0 | Lenguaje principal | ⚠️ EOL (PHP 5.6 desde 2018) | 🔴 Crítico |
| Yii 2 | ~2.0.14 | Framework web/REST | Mantenimiento mínimo | 🟡 Medio |
| yii2-bootstrap4 | ~2.0.0 | UI (solo SiteController) | Vigente | 🟢 Bajo |
| yii2-swiftmailer | ~2.0.0 | Mailer (no usado activamente) | Deprecado | 🟡 Medio |
| yii2-debug | ~2.1.0 | Debug toolbar (dev) | Vigente | 🟢 Bajo |
| yii2-gii | ~2.2.0 | Generador de código (dev) | Vigente | 🟢 Bajo |
| Codeception | ^4.0 | Testing | Vigente | 🟢 Bajo |
| Apache | — | Web server (via Docker) | Vigente | 🟢 Bajo |
| Docker | — | Contenedor de despliegue | Vigente | 🟢 Bajo |
| OpenSSL AES-128-ECB | — | Cifrado de credenciales en tránsito | Vigente (modo ECB ⚠️) | 🟡 Medio |

## Notas de riesgo

> [!danger] PHP 5.6 EOL
> El `composer.json` declara `"php": ">=5.6.0"`. PHP 5.6 llegó a EOL en diciembre de 2018. Si el entorno de producción corre una versión moderna (7.x / 8.x) esto no es un problema práctico, pero el requisito mínimo no está actualizado.

> [!warning] AES-128-ECB
> El modo ECB (Electronic Codebook) es **determinístico**: el mismo texto plano siempre produce el mismo cifrado. No es adecuado para datos con patrones repetitivos. Debería migrarse a AES-CBC o AES-GCM.

> [!warning] Credenciales hardcodeadas en main.php
> Varias URLs base, usuarios, contraseñas y la `passOpenSSL` están hardcodeadas en `config/main.php`. Ver [[security-inventory]].

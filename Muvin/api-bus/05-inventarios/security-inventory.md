# Seguridad — Inventario de Riesgos

> **Última revisión:** 2026-04-29

| # | Hallazgo | Módulo | Severidad | Descripción |
|---|---|---|---|---|
| S-01 | Credenciales hardcodeadas — `passOpenSSL` silohub | silohub | 🔴 Crítico | `'passOpenSSL' => 'p7^W:E[9[%FkYx9Rtu3ZH~N8?a(eCwDu'` en `config/main.php` |
| S-02 | Credenciales hardcodeadas — `passOpenSSL` timbues | timbues | 🔴 Crítico | `'passOpenSSL' => '9TL69DM>}r]grz\`WB]{-xb8yh_{rNK6['` en `config/main.php` |
| S-03 | Credenciales triviales Zarcam | agroquimicos | 🔴 Crítico | `usernameZarcam = 'admin'`, `secretZarcam = 'admin'` en `config/main.php` |
| S-04 | `client_secret = 'secret'` SiloHub | silohub | 🔴 Crítico | Credencial OAuth2 trivial hardcodeada |
| S-05 | SSL deshabilitado en cURL | common | 🔴 Crítico | `verifyHost = false`, `verifyPeer = false` en `BaseCurl` — todas las conexiones externas son vulnerables a MITM |
| S-06 | CORS `Origin: ['*']` | monsanto, common | 🟡 Medio | Acepta requests de cualquier origen |
| S-07 | AES-128-ECB para cifrado de credenciales | silohub, timbues | 🟡 Medio | Modo ECB es determinístico, no recomendado para datos sensibles |
| S-08 | URL entorno dev en producción | agroquimicos | 🟡 Medio | `urlBaseSanMiguel = 'https://desa3.securityconsultant.com.ar:8081'` — URL `desa3` posiblemente no sea producción |
| S-09 | `empresaSiloHub = 'PRU'` | silohub | 🟡 Medio | Valor que parece ser "PRUeba" hardcodeado — ⚠️ Pendiente de verificar si es correcto en producción |
| S-10 | Sin rate limiting ni throttling | todos | 🟡 Medio | No hay protección contra abuso de los endpoints proxy |
| S-11 | CSRF y cookies deshabilitadas globalmente | config | 🟢 Bajo | `enableCsrfValidation = false` — aceptable para API REST stateless, pero documentar |
| S-12 | `gii` habilitado en dev con `allowedIPs = ['*']` | config | 🟡 Medio | Generador de código accesible desde cualquier IP en entorno dev |

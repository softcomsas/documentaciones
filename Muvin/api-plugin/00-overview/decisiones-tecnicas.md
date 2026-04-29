# Decisiones Técnicas — plugin-muvin-cupos

## ADR-001: Yii2 como framework base

**Decisión:** Usar Yii2 PHP para el plugin.

**Contexto:** El ecosistema Muvin ya usa Yii2 en sus backends legacy (ms-legacy, ms-commercial). Reutilizar el framework reduce la curva de aprendizaje del equipo.

**Consecuencias:**
- ✅ Consistencia con el resto del stack
- ✅ ActiveRecord disponible para modelos
- ⚠️ Yii2 no está en mantenimiento activo (el equipo migró a NestJS en la plataforma principal)

---

## ADR-002: Método almacenado en BD como callable

**Decisión:** El campo `proceso.metodo` contiene el nombre de la clase/método a invocar (ej: `"ProcesoAca::DescargarMails"`), y se llama vía `call_user_func()`.

**Contexto:** Permite agregar nuevos procesos sin desplegar código, solo insertando registros en la BD.

**Consecuencias:**
- ✅ Configuración dinámica sin código
- 🔴 **Riesgo de seguridad crítico**: si la BD es comprometida, un atacante puede ejecutar código PHP arbitrario
- ⚠️ No hay validación del callable antes de ejecutarlo

---

## ADR-003: Un proceso por empresa/formato

**Decisión:** Cada cerealera tiene su propia clase `Proceso*.php` con lógica de parsing específica.

**Contexto:** Cada empresa envía los cupos en formatos distintos (PDF, HTML con estructura propia, Excel). No fue posible unificar el parser.

**Consecuencias:**
- ✅ Aislamiento de cambios: modificar el parser de Bunge no afecta a ACA
- ⚠️ Duplicación de código en la lógica común (conexión IMAP, guardado en BD)
- ⚠️ 9 procesos paralelos que mantener

---

## ADR-004: Cron cada 15 minutos

**Decisión:** El cron corre `yii parametros && yii lector` cada 15 minutos.

**Contexto:** Los cupos son time-sensitive (ventanas de carga). 15 minutos es el compromiso entre latencia y carga en el servidor de email.

**Consecuencias:**
- ✅ Latencia máxima de 15 min desde recepción del email hasta carga en Muvin
- ⚠️ Si un proceso tarda más de 15 min, la siguiente ejecución se superpone (no hay lock distribuido visible)

---

## ADR-005: Credenciales hardcodeadas en ServiciosMuvin.php

**Decisión (implícita):** Las credenciales del usuario de sistema de Muvin API están hardcodeadas directamente en la clase.

**Contexto:** Decisión de conveniencia en una etapa temprana del proyecto.

**Consecuencias:**
- 🔴 **Riesgo crítico de seguridad**: credenciales expuestas en el código fuente
- 🔴 Rotación de credenciales requiere redespliegue
- ⚠️ Incompatible con buenas prácticas de secrets management

**Recomendación:** Mover a variables de entorno (`$_ENV['MUVIN_USER']`, `$_ENV['MUVIN_PASS']`, `$_ENV['MUVIN_URL']`) o a un vault de secretos.

---

## ADR-006: AES-128-ECB para encriptación de passwords de clientes

**Decisión:** Las contraseñas de `Cliente` se encriptan con `openssl_encrypt(..., 'AES-128-ECB', ...)`.

**Contexto:** Se requería almacenar las contraseñas de acceso a Muvin encriptadas para poder desencriptarlas en tiempo de ejecución (no se puede usar hash unidireccional porque la contraseña se usa para login).

**Consecuencias:**
- ⚠️ AES-ECB es un modo inseguro: no usa IV, patrones en el plaintext se reflejan en el ciphertext
- 🔴 Recomendación: migrar a AES-256-GCM o almacenar en un vault de secretos

---

## ADR-007: PluginV2 como refactorización parcial

**Decisión:** Se creó `PluginV2/` con `BaseProcess` abstracto y DTOs, pero solo para DescargasRT. El resto del código no fue migrado.

**Contexto:** El equipo intentó mejorar la arquitectura para los nuevos procesos RT (2025) sin refactorizar todo el código existente.

**Consecuencias:**
- ✅ Los nuevos procesos tienen mejor estructura
- ⚠️ Coexistencia de dos estilos arquitectónicos en el mismo proyecto
- ⚠️ Deuda técnica: los procesos viejos deberían migrar a PluginV2

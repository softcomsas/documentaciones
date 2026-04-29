# Módulo PluginV2 — DescargasRT

**Ruta:** `source/components/PluginV2/`

## Propósito

`PluginV2` es una refactorización más estructurada del plugin, introducida en 2025 para manejar los procesos de **Descargas en Tiempo Real (RT)**. A diferencia del módulo `Plugin/Cupos/`, usa clases abstractas, DTOs explícitos y separación de responsabilidades.

## Estructura

```
PluginV2/
├── BaseProcess.php                    ← clase base abstracta
├── MailClient.php                     ← cliente IMAP
├── dto/
│   ├── EmailDto.php                   ← DTO de un email
│   └── ResultParseDto.php             ← DTO de resultado de parseo
├── parsers/
│   ├── DescargaRtParserFile.php       ← parser para archivos RT
│   └── ParseError.php                 ← clase de error de parseo
├── procedures/
│   └── DescargaRtProcess.php          ← proceso DescargasRT
└── utils/
    └── (utilidades)
```

## Clase BaseProcess

```php
abstract class BaseProcess extends BaseObject {
    private $_config;    // ClienteConfiguracion
    private $_client;    // Cliente
    private $_mailClient; // MailClient

    public function run() {
        $emails = $this->mailClient->searchEmailsUnread();
        foreach ($emails as $email) {
            $emailDto = $this->mailClient->getEmail($email);
            if (in_array($emailDto->subject, $this->subjectToIgnore())) continue;
            $this->processEmail($emailDto);
        }
    }

    abstract public function processEmail($emailDto);
    
    public function reportErrors($emailDto, $errors) { ... }
    protected function subjectToIgnore(): array { return []; }
}
```

### Responsabilidades de BaseProcess
- Conectar al servidor IMAP vía `MailClient`
- Iterar emails no leídos
- Filtrar por asunto ignorado
- Delegar el procesamiento a la subclase (`processEmail`)
- Reportar errores vía email

## DescargaRtProcess

La única subclase concreta actualmente. Extiende `BaseProcess` e implementa `processEmail()`:

1. Obtiene los adjuntos del `EmailDto`
2. Invoca `DescargaRtParserFile` para parsear el archivo
3. Si hay errores, llama a `reportErrors()`
4. Si el parseo es exitoso, guarda en la tabla `descargas_rt`
5. Llama a `ServiciosDescargas` para notificar a la plataforma Muvin

## DTOs

### EmailDto
```php
class EmailDto {
    public string $subject;
    public string $from;
    public string $body;
    public array $attachments; // array de archivos adjuntos
    public string $date;
}
```

### ResultParseDto
```php
class ResultParseDto {
    public bool $success;
    public array $data;      // datos parseados
    public array $errors;    // errores de parseo
}
```

## MailClient

Encapsula la conexión IMAP y proporciona:
- `searchEmailsUnread()` → array de IDs de emails no leídos
- `getEmail($id)` → `EmailDto`
- Manejo de conexión/desconexión

## Comparación con Plugin/Cupos/

| Aspecto | Plugin/Cupos/ (v1) | PluginV2 |
|---|---|---|
| Estructura | Clases estáticas planas | Clases abstractas con herencia |
| DTOs | No hay DTOs formales | `EmailDto`, `ResultParseDto` |
| IMAP | Directo en cada proceso | Encapsulado en `MailClient` |
| Testing | Difícil (métodos estáticos) | Más testeable (inyección de dependencias) |
| Procesos | 7 empresas | Solo DescargasRT (por ahora) |
| Estado | Legacy activo | Activo y en crecimiento |

## Ruta de migración

Los procesos de `Plugin/Cupos/` deberían migrar gradualmente a `PluginV2/`:
1. Crear `ProcesoAcaV2 extends BaseProcess`
2. Mover la lógica de parseo a `parsers/AcaParserFile.php`
3. Eliminar la clase estática original

# Módulo Plugin/Cupos — Procesos por Empresa

**Ruta:** `source/components/Plugin/Cupos/`

## Propósito

Este módulo contiene toda la lógica de descarga, parseo y carga de cupos para cada cerealera. Cada empresa tiene su propia clase `Proceso*.php` porque el formato de sus notificaciones de cupo es diferente.

## Estructura del módulo

```
Plugin/Cupos/
├── ProcesoAca.php              ← ACA: PDF
├── ProcesoBunge.php            ← Bunge: HTML
├── ProcesoCofco.php            ← Cofco + Tomas Hnos/Cofco: HTML
├── ProcesoDreyfus.php          ← Dreyfus + Tomas Hnos/Dreyfus: HTML
├── ProcesoFyo.php              ← Tomas Hnos/Fyo: HTML
├── ProcesoMolinosAgro.php      ← MolinosAgro: HTML
├── ProcesoSamsa.php            ← SAMSA: HTML
├── ProcesoDescargasRT.php      ← DescargasRT v1
├── ProcesoDescargasRT2.php     ← DescargasRT v2
├── ServiciosMuvin.php          ← cliente HTTP a Muvin API
├── ServiciosDescargas.php      ← cliente HTTP para descargas RT
├── LeerPdf.php                 ← parser de PDFs
├── LeerPdfDescargas.php        ← parser de PDFs para descargas
├── LeerExcel.php               ← parser de Excel
├── LeerHtmlBungue.php          ← parser HTML Bunge
├── LeerHtmlCofco.php           ← parser HTML Cofco
├── LeerHtmlMolinosAgro.php     ← parser HTML MolinosAgro
├── LeerHtmlSamsa.php           ← parser HTML Samsa
├── LeerHtmlTomasHnosFyo.php    ← parser HTML Tomas Hnos/Fyo
├── Destinos.php                ← sincronización destinos
├── Productos.php               ← sincronización productos
├── CuitEmpresas.php            ← sincronización CUITs
├── OnccaDestinosProductos.php  ← sincronización ONCCA
└── Herramientas.php            ← utilidades (caracteres especiales, etc.)
```

## Clases de modelos relacionadas

```
models/Cupos/
├── CupoDocumento.php     ← registro de un cupo parseado (tabla cupo_documento)
├── CupoCodigo.php        ← códigos extraídos de cartas porte (tabla cupo_codigo)
└── objCupoMuvin.php      ← DTO de cupo para enviar a Muvin API
```

## Patrón común de los Proceso*.php

Todos los procesos siguen el mismo patrón estático:

```php
class ProcesoXxx {
    static public function DescargarMails(ClienteConfiguracion $client_conf) {
        // 1. Conectar a IMAP con credenciales del client_conf
        // 2. Buscar emails no leídos (por asunto/remitente)
        // 3. Por cada email:
        //    a. Descargar adjuntos
        //    b. Parsear adjunto (LeerPdf/LeerHtml/LeerExcel)
        //    c. Guardar CupoDocumento en BD con estado PENDIENTE
        //    d. Llamar CargarMuvin($client_conf, $cupoDoc)
        //    e. Actualizar estado a PROCESADO o ERROR
        // 4. Marcar email como leído
        // 5. Retornar resumen
    }
    
    static private function CargarMuvin(ClienteConfiguracion $client_conf, CupoDocumento $cupo) {
        // 1. Obtener credenciales Muvin del Cliente
        // 2. ServiciosMuvin::login($user, $pass)
        // 3. ServiciosMuvin::cargarCupo($cupoObj)
        // 4. Guardar respuesta
    }
}
```

## Descripción por proceso

### ProcesoAca
- **Empresa:** ACA (Asociación de Cooperativas Argentinas)
- **Formato:** PDF adjunto en el email
- **Parser:** `LeerPdf.php`
- **ID proceso BD:** 1

### ProcesoBunge
- **Empresa:** Bunge Argentina
- **Formato:** HTML en el cuerpo o adjunto del email
- **Parser:** `LeerHtmlBungue.php`
- **ID proceso BD:** 3

### ProcesoCofco
- **Empresa:** Cofco International (y Tomas Hnos/Cofco)
- **Formato:** HTML
- **Parser:** `LeerHtmlCofco.php`
- **ID proceso BD:** 5 (Cofco directo), hardcoded id=20 (Tomas Hnos/Cofco)

### ProcesoDreyfus
- **Empresa:** Louis Dreyfus (y Tomas Hnos/Dreyfus)
- **Formato:** HTML
- **ID proceso BD:** 2 (Dreyfus directo), hardcoded id=24 (Tomas Hnos/Dreyfus)

### ProcesoSamsa
- **Empresa:** SAMSA
- **Formato:** HTML
- **Parser:** `LeerHtmlSamsa.php`
- **ID proceso BD:** 4

### ProcesoMolinosAgro
- **Empresa:** Molinos Agro
- **Formato:** HTML
- **Parser:** `LeerHtmlMolinosAgro.php`
- **ID proceso BD:** 6

### ProcesoFyo
- **Empresa:** Tomas Hnos/Fyo
- **Formato:** HTML
- **Parser:** `LeerHtmlTomasHnosFyo.php`
- **ID configuración BD:** hardcoded 26

## Clases de sincronización de catálogos

| Clase | Método | Tabla local | Fuente |
|---|---|---|---|
| `Productos` | `CargarProductos()` | `productos` | Muvin API |
| `Destinos` | `CargarDestinos()` | `destinos` | Muvin API |
| `CuitEmpresas` | `CargarCuitEmpresas()` | `cuit_empresas` | Muvin API |
| `OnccaDestinosProductos` | sincronizar | `oncca_destino_producto` | Muvin API |

Estas clases son invocadas por el comando `yii parametros` antes de cada ciclo de procesamiento.

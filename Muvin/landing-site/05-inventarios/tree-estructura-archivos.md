# Árbol de Estructura de Archivos — Landing Site Muvin

> **Fuente:** repositorio `landing-site-main/landing-site`
> **Fecha de análisis:** 2026-05-05

```
landing-site/
├── .gitignore                          # Archivos excluidos del repo (incluye .env presumiblemente)
├── docker-compose.yml                  # Orquestación Docker de los 3 servicios
├── README.md                           # Instrucciones de variables de entorno (.env)
├── all-in-one-wp-migration.6.77.zip    # ⚠️ Binario del plugin de backup (no debería estar en git)
└── nginx-conf/                         # Configuración de Nginx
    ├── nginx.conf                      # ✅ Config activa en producción (183 líneas)
    ├── nginx.conf.new                  # ⚠️ Config candidata/nueva — NO activa (163 líneas)
    └── allowip.ip                      # 🔒 Whitelist de IPs para wp-login (contenido no versionado visible)
```

## Archivos NO versionados (inferidos)

| Archivo | Propósito | Por qué no está en repo |
|---------|-----------|------------------------|
| `.env` | Credenciales MySQL (ROOT, USER, PASSWORD) | Seguridad — contiene secretos |
| `/opt/landingpage/wordpress/` | Instalación completa de WordPress | Volumen del host, no código |
| `/opt/landingpage/dbdata/` | Datos MySQL | Datos de producción, no código |
| `wp-config.php` | Config WP con credenciales DB | Generado por imagen Docker, contiene secretos |

## Notas

- 📦 **El código fuente de WordPress NO está versionado** — se usa la imagen oficial Docker. Los temas, plugins adicionales y uploads no son visibles en el repositorio.
- ⚠️ **`all-in-one-wp-migration.6.77.zip`** — binario de 6.77 MB versionado en Git. Deberían gestionarse con gestores de paquetes o descarga en CI/CD.
- ⚠️ **`nginx.conf.new`** — la existencia de una configuración alternativa sin desplegar es un riesgo de confusión operativa. Debe definirse cuál es la canónica.

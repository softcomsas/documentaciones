# Endpoints — documentacion-app (Yii2 PHP)

> **Base URL:** `http://<host>:<port>/api/`
> **Autenticación:** ⚠️ Pendiente de verificar
> **Última revisión:** 2026-04-29
> **Consumido por:** [[modulo-documentacion]]

## Recursos REST (inferidos de controladores)

| Controlador | Ruta estimada | Métodos | Entidad |
|---|---|---|---|
| `CategoriaController` | `/categoria` | GET, POST, PUT, DELETE | [[entidad-categoria]] |
| `CategoriaTipoDocController` | `/categoria-tipo-doc` | GET, POST, PUT, DELETE | Tipo de documento por categoría |
| `DocumentoController` | `/documento` | GET, POST, PUT, DELETE | [[entidad-documento]] |
| `DocumentoTipoController` | `/documento-tipo` | GET, POST, PUT, DELETE | Tipo de documento |
| `EmpresaAsocController` | `/empresa-asoc` | GET, POST, PUT, DELETE | Empresa asociada |
| `EntidadController` | `/entidad` | GET, POST, PUT, DELETE | [[entidad-entidad]] |
| `EntidadTipoDocumentoController` | `/entidad-tipo-documento` | GET, POST, PUT | Tipos de doc por entidad |
| `InfoUserController` | `/info-user` | GET | Información del usuario |
| `MotivoRechazoController` | `/motivo-rechazo` | GET | Catálogo motivos de rechazo |
| `PresentacionEstadoController` | `/presentacion-estado` | GET, POST, PUT | Estado de presentación de documentos |
| `RecursosExternosController` | `/recursos-externos` | GET | ⚠️ Propósito a verificar |
| `SiteController` | `/site/login`, `/site/logout` | POST | Autenticación Yii2 |
| `UsuarioEntidadHijaController` | `/usuario-entidad-hija` | GET, POST, PUT, DELETE | Relación usuario↔entidad hija |
| `VencimientosController` | `/vencimientos` | GET | Documentos próximos a vencer |

## Notas

- ⚠️ El manejo de archivos adjuntos (documentos físicos) no es claro desde los controladores. Puede estar en el `DocumentoController` con un endpoint de upload multipart.
- ⚠️ `UsuarioEntidadHijaController` sugiere una jerarquía de entidades (padre/hija) que no está documentada.
- ⚠️ `RecursosExternosController` — propósito exacto pendiente de revisión.

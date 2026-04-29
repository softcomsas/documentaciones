# F-07 — Carga TXT

## Descripción

Permite cargar cupos de descarga de forma masiva mediante un archivo `.TXT` con un formato específico. Alternativa a la carga manual cupo por cupo desde la pantalla de Gestión de Cupos.

## Ubicación

| Capa | Ruta |
|---|---|
| Frontend (dialog) | `descargas-app/frontend/src/app/components/upload-file/upload-file.component.ts` |
| Backend (controller) | `descargas-app/backend/src/controllers/txt-upload.controller.ts` |
| Backend (servicio) | `descargas-app/backend/src/services/upload-txt.service.ts` |
| Almacenamiento temp | `descargas-app/backend/uploads/` |

## Ruta de apertura

Se abre como dialog (`MatDialog`) desde la pantalla principal de Gestión de Cupos (`UploadFileComponent`).

## Endpoints backend

| Método | Ruta | Descripción |
|---|---|---|
| `POST` | `/txt-upload/upload` | Cargar archivo TXT de cupos |
| `POST` | `/txt-upload/upload-terminales` | Cargar archivo TXT de terminales |

## Validaciones del archivo

```typescript
// Solo acepta extensión .txt
if (fileExt !== '.txt') 
  throw new BadRequestException('Por favor, seleccione un archivo con extensión .TXT');
```

- Extensión obligatoria: `.txt`
- El archivo se sube al servidor con nombre aleatorio (hash de 32 caracteres)
- El servicio `TxtUploadService.readFileData()` parsea el archivo y carga los datos

## Flujo

```
1. Usuario abre el dialog "Cargar TXT" desde la pantalla principal
2. Selecciona el archivo .txt
3. POST /txt-upload/upload (multipart/form-data)
4. Backend valida extensión
5. Multer guarda el archivo en uploads/ con nombre aleatorio
6. TxtUploadService.readFileData() lee y procesa el contenido
7. Los cupos son insertados en la BD
8. Retorna { status: 200, message: "..." }
```

## Estructura esperada del TXT

El formato del archivo TXT es específico de AFIP/SCE. Contiene registros de cupos con cabecera (`ArchivoExternoCabecera`) y líneas de detalle. El modelo `ArchivoExternoCabecera` define la estructura de la cabecera del archivo.

## Consideraciones

- Los archivos subidos no se limpian automáticamente del directorio `uploads/`. Puede crecer indefinidamente.
- Errores de validación en el archivo son retornados como mensajes de texto (se limpian los prefijos `Validation error: `).

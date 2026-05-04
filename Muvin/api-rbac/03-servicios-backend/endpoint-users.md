# Endpoints: Users

> **Tag:** Users | **Auth:** `Authorization: {seed}` en todos

## GET `/user`
Lista usuarios activos. Acepta query params de filtro.
- **200:** Array de User

## GET `/user/{id}`
- **200:** User object
- **404:** No encontrado o eliminado

## POST `/user`
```json
{ "name": "string (min3, max50)", "password": "string (min6, max50)" }
```
- **201:** ID del nuevo usuario
- **422:** Errores de validación

## PUT `/user/{id}`
```json
{ "name": "string?", "password": "string?" }
```
- **204:** OK
- **422:** Errores de validación

## DELETE `/user/{id}`
Soft delete (`deleted_at = time()`).
- **204:** OK
- **404:** No encontrado

## GET `/user/by-roles?roles_id={id}`
Usuarios con el rol indicado.
- **200:** Array de User

## POST `/user/login`
```json
{ "username": "string", "password": "string" }
```
- **200:** `{ id, name, token }`
- **422:** Credenciales incorrectas

## POST `/user/decode-token`
```json
{ "token": "eyJ..." }
```
- **200:** `{ id: integer }`
- **422:** Token inválido

## PUT `/user/change-password`
```json
{ "old_password": "string", "new_password": "string" }
```
- **204:** OK
- **422:** Errores

## POST `/user/check-access-to-service`
```json
{ "id_user": 1, "service": "string", "type": "GET|POST|PUT|DELETE" }
```
- **200:** `true` o `false`
- **422:** Errores de validación

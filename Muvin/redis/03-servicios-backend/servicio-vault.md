# Servicio: HashiCorp Vault

## Descripción

Vault es el gestor centralizado de secretos utilizado para almacenar y distribuir las credenciales SSH de deploy. El runner de GitHub Actions autentica contra Vault y recupera los secretos necesarios antes de conectarse al servidor.

## Endpoint

```
https://vault.alternativasinteligentes.com
```

## Ruta de secretos usada

```
muvin/data/dev_ssh
```

| Clave en Vault | Variable de entorno | Descripción |
|---------------|---------------------|-------------|
| `SSH_HOST` | `SSH_HOST` | IP o hostname del servidor dev |
| `SSH_PRIVATE_KEY` | `SSH_PRIVATE_KEY` | Clave privada SSH (formato PEM) |
| `SSH_USER` | `SSH_USER` | Usuario de conexión SSH |

## Autenticación desde GitHub Actions

```yaml
- uses: hashicorp/vault-action@v2.4.0
  with:
    url: https://vault.alternativasinteligentes.com
    token: ${{ secrets.VAULT_TOKEN }}
    secrets: |
      muvin/data/dev_ssh SSH_HOST;
      muvin/data/dev_ssh SSH_PRIVATE_KEY;
      muvin/data/dev_ssh SSH_USER;
```

El `VAULT_TOKEN` se guarda como GitHub Secret y nunca aparece en los logs del workflow.

## Ventaja sobre el enfoque de config-deploys

> [!success]
> A diferencia de `config-deploys` donde las contraseñas SSH se almacenan como variables de GitLab CI y se exponen en `ps aux` via `sshpass`, aquí las credenciales viven en Vault y se usan mediante clave SSH. Esto es **la forma correcta** de gestionar secretos de deploy.

## Referencias

- [[modulo-deploy-dev]]
- [[arquitectura-alto-nivel]]

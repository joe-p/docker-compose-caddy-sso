This repository contains a [docker-compose.yml](docker-compose.yml) file that can be used as a template for setting up automatic TLS and SSO with caddy. 

# Configuration
## .env
[.env](.env) contains variables to be used in [docker-compose.yml](docker-compose.yml)

| Variable | Description |
| --- | --- |
| CONFIGS_DIR | Where config files, such as [Caddyfile](configs/caddy/Caddyfile) are saved |
| VOLUMES_DIR | Where mounted container volumes are saved |

## common.env
[common.env](common.env) contains environment variables that can be applied to multiple containers.

| Variable | Description |
| --- | --- |
| TZ | Timezone to use in the container |

## secrets.env
secrets.env contains potentially sensistive data that you might not want to expose publicly. See [example-secrets.env](example-secrets.env) for an example. 

| Variable | Description |
| --- | --- |
| EMAIL | The email used to create the admin account (this should be your email) |
| DOMAIN | Your domain name. This is used to create subdomains. For example `DOMAIN=example.com` results in the auth url being `https://auth.example.com` |
| CODE | The registration code someone needs to register as a user |

# Registration
To register, simply go to `https://auth.$DOMAIN` and click on the register button. Once you register you will get a verification email sent to you. Be on the lookout for an email with the subject `User Registration for $DOMAIN` from `caddy@$DOMAIN` (it will likely go to your spam folder). Once you have gone through the verification flow, the container needs to be restarted. If you are the only person who will be registering you can delete the `mail` service from [docker-compose.yml](docker-compose.yml).

# Adding Services
To add additional services simply add the subdomain to the [Caddyfile](configs/caddy/Caddyfile):

```
additional_service.{$DOMAIN} {
    authorize with admin_policy
    reverse_proxy additional_service:80
}
```

Here `additional_service` is the name of the service in your [docker-compose.yml](docker-compose.yml) file. If you want to add a service that doesn't require authorization, simply omit the `authorize with admin_policy` line. 
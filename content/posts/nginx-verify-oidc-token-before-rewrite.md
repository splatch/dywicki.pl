---
author: splatch
category:
  - software
cover:
  alt: Nginx logo
  image: /wp-content/uploads/2020/11/nginx-logo.png
date: "2020-11-08T22:26:42+00:00"
guid: http://dywicki.pl/?p=1004
summary: A while ago I managed to get Nginx and OpenID Connect working together. Obviously, there is a commercial plugin provided by Nginx authors, but for simpler cases we can use a a simpler way with `auth_request` which works nicely with OIDC token introspection served by for example Keycloak.
tag:
  - docker
  - nginx
title: 'Nginx: verify OIDC token before rewrite'
url: /2020/11/nginx-verify-oidc-token-before-rewrite/

---
A while ago I managed to get Nginx and OpenID Connect working together. Obviously, there is a commercial plugin provided by Nginx authors, but for simpler cases we can use a a simpler way with `auth_request` which works nicely with OIDC token introspection served by for example Keycloak.

### Background for work

For some time I had a case where I had to cover legacy service which used HTTP basic auth and combine it with new one which used proper OAuth2. For that I combined nginx rewrite module with `auth_request` rule.

### Config recipe

```
    location /rest/ {
        auth_request /_auth;

        proxy_set_header   Authorization "Basic ${SERVER_AUTH}";
        proxy_pass         ${SERVER_URI};
    }

    location /api/ {
        rewrite ^/api/(.*) $1  break;
        proxy_pass         ${API_URI}$uri$is_args$args;
    }

    location = /_auth {
        internal;
        proxy_pass_request_body off;
        proxy_method      POST;
        proxy_set_header  Authorization "Basic ${OIDC_CLIENT_AUTH}";
        proxy_set_header  Content-Type "application/x-www-form-urlencoded";
        proxy_set_body    "token=$http_x_api_key&token_hint=access_token";
        proxy_pass ${OIDC_INTROSPECT_URI};
    }
```

In above case there are 5 environment variables, out of which two are basic auth ceredentials:

- `SERVER_AUTH` \- basic authentication to be injected for legacy service.
- `SERVER_URI` \- legacy service location.
- `API_URI` \- location of new service(s).
- `OIDC_CLIENT_AUTH` \- basic authentication for nginx client in OAuth 2/OIDC server aka identity provider.
- `OIDC_INTROSPECT_URI` \- token validation address

In above situation the `/rest` endpoint is rewritten to legacy service. What is remarkable - this endpoint uses `X-API-KEY` header which is then forwarded to token introspection via `$http_x_api_key` request variable.  
I can't remember right now why, but `Authorization` header didn't work with `auth_check` for some reason.

### How does it work

Once configuration was set and started working it turns to be trivial. Getting there.. is usual hours long search for working snipped across various versions of Nginx.  
In above example any call coming to `/rest` gets forwarded to internal `/_auth` location which is invisible over regular HTTP port. Answer from this endpoint different than 200 indicates that authentication have failed.  
This means that standard HTTP error codes such `401`/ `403`/ `500` will instruct nginx to hold processing and return error to the caller.

### Dockerfile

The last point is making it working with Docker. While working on this issue I learned that it is possible to have environment inject with Nginx image.

All you have to do is to locate your config which uses environment variables in `/etc/nginx/templates/default.conf.template`. By default when spinning new instance Nginx will pre-process config and substitute `${PLACEHOLDERS}` with their values pushed via docker env.

I know you probably went over dozen of configuration fragments before coming here and you just got one more to test. Hope its gonna work for you!

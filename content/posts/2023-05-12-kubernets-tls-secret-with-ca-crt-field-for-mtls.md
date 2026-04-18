---
author: splatch
category:
  - kubectl
  - kubernetes
cover:
  alt: Computer screen displaying code and terminal output
  image: https://images.unsplash.com/photo-1774901128283-64c62117216a?auto=format&fit=crop&fm=jpg&ixlib=rb-4.1.0&q=80&w=1600
date: "2023-05-12T08:39:00+00:00"
guid: http://dywicki.pl/?p=1020
summary: By default kubernetes tls secret must consist two fields a `tls.key` and `tls.crt`. It works perfectly fine in most of the cases, but not when we need mutual tls which is handled by ingress-nginx. This kind of ingress requires a secret with a "ca.crt" field to validate client certs. Obviously it tricks a kubectl which can't append more than standard.
tag:
  - linux
title: Kubernets TLS secret with ca.crt field for mtls
url: /2023/05/ingress-nginx-and-tls-secret-with-ca-crt-field/

---
By default kubernetes tls secret must consist two fields a `tls.key` and `tls.crt`. It works perfectly fine in most of the cases, but not when we need mutual tls which is handled by ingress-nginx. This kind of ingress requires a secret with a "ca.crt" field to validate client certs. Obviously it tricks a kubectl which can't append more than standard.

Our help in this case will be `kubectl patch`. The patch command allows to replace or append new parts to resource definitions. In my case certificate and key file is generated with openssl, thus I wish to populate extra field in order to keep ingress-nginx happy. One note - if you miss `ca.crt` field in secret pointed by `nginx.ingress.kubernetes.io/auth-tls-secret annotation`, ingress will keep working. It will just not perform any mtls validation. It will emit a warning, but that's all.

If you wonder how to make it happen, this is how I managed it:

```bash
#!/bin/bash
namespace="foo"
keyFile="my-custom-ca.key"
crtFile="my-custom-ca.crt"
secret="client-certificate-ca"

kubectl -n "$namespace" create secret tls "$secret" --key=$keyFile \
  --cert=$crtFile || (echo "Could not create $secret in namespace $namespace" && exit 1)
caContents=$(base64 -w0 $crtFile)
kubectl -n "$namespace" patch secret "$secret" -p "{\"data\":{\"ca.crt\":\"${caContents}\"}}"
```

This little thing keeps it as a single secret. Obviously you might not wish to do that in case of production setups, but that's another thing. :-)

Additional point - if you are running macos with its flavor of shell, or an ancient linux distro, then `base64 -w0` call will fail. In such case you can use `kubectl get secret $secret` with jsonpath output or with [go-template](/2023/05/one-shot-retrieval-of-tls-certificates-from-kubernetes/) trick I described in my previous post.

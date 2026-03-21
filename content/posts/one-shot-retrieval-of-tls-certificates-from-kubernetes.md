---
author: splatch
category:
  - kubectl
  - kubernetes
date: "2023-05-03T07:41:00+00:00"
guid: http://dywicki.pl/?p=1015
summary: I believe that I am not only one who needed to get a secret value out of kubernetes. Usual advice focus on use of `kubectl` with json output and/or jsonpath. Problem is - the `kubectl` jsonpath is limited thus result needs to be scanned further by jq. It works, but jq is not part of everyone's working environment.
tag:
  - kubectl
  - secrets
title: One shot retrieval of TLS certificates from Kubernetes
url: /2023/05/one-shot-retrieval-of-tls-certificates-from-kubernetes/

---
I believe that I am not only one who needed to get a secret value out of kubernetes. Usual advice focus on use of `kubectl` with json output and/or jsonpath. Problem is - the `kubectl` jsonpath is limited thus result needs to be scanned further by jq. It works, but jq is not part of everyone's working environment.

While looking for solution I found a blog post titled " [Retrieve TLS certificates from Kubernetes](https://blog.kubovy.eu/2020/05/16/retrieve-tls-certificates-from-kubernetes/)", which partially answered my question. Sadly it used both json and jq. It took me a bit to find out that `kubectl` output I have is not something which will be easy for jsonpath flavor supported by `kubectl` itself:

```
{
  "apiVersion": "v1",
  "data": {
    "ca.crt": "LS0tLS1CRUdJTiBDRV...",
    "tls.crt": "LS0tLS1CRUdJTiBDRV...",
    "tls.key": "LS0tLS1CRUdJTiBSU0..."
  },
  "kind": "Secret",
  "metadata": {
    "annotations": {
      "kubectl.kubernetes.io/last-applied-configuration": "...."
    },
    "creationTimestamp": "2023-05-02T14:42:00Z",
    "labels": {
      "app.kubernetes.io/managed-by": "..."
    },
    "name": "client-root-ca-certificate",
    "namespace": "default",
    "resourceVersion": "16702",
    "uid": "04540ad3-96c4-4989-bb71-cd9fc6cda3a1"
  },
  "type": "kubernetes.io/tls"
}
```

This is due to fact that `data` is an object and all filtering capabilities embedded in `kubectl` jsonpath implementation supports some conditions, but it assumes list, hence filtering list objects. A common example of filtering expressions such as `-o jsonpath="{.data[?(@. ...)]}"` will not work, because `@` element is an object inside of the list. There is no way to make a test expression against map key (or object property) itself. This seems to be a gap in initial json-path proposal which is addressed by jq.  
In theory we could compare beginning of base64 representation, but it is not a reliable in situations where we have two elements in secret containing certificate. Beginning of base64 encoded certificate is always the same.

Luckily a quick inspection of `kubectl` output format revealed "go-template" output which seemed more flexible in constructing conditions.

```
kubectl get secret client-root-ca-certificate -ogo-template='{{index .data "ca.crt" }}'
```

Above example gets a "data" which is map and then reads a "ca.crt" key out of it. This pattern will work also for other secrets. Without jq and complicated jsonpath. ;)

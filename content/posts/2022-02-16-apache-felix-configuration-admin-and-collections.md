---
author: splatch
category:
  - osgi
date: "2022-02-16T12:51:04+00:00"
guid: http://dywicki.pl/?p=1011
summary: A long time ago I wrote an post about [Configuration Admin and Arrays](/2015/02/apache-felix-configuration-admin-with-array-values/). Recently I attempted to use this feature with openHAB and I found that it doesn't work with its configuration framework.
tag:
  - config-admin
  - karaf
title: Apache Felix Configuration Admin and collections
url: /2022/02/apache-felix-configuration-admin-and-collections/

---
A long time ago I wrote an post about [Configuration Admin and Arrays](/2015/02/apache-felix-configuration-admin-with-array-values/). Recently I attempted to use this feature with openHAB and I found that it doesn't work with its configuration framework.

Locating issue was rather fast as I got HTTP 500 while retrieving configuration through openHAB rest API. After implementing a basic patch to properly map parameter values which come as an array I attempted modification through openHAB user interface. I found that reading works, however what I wrote initially as an array:

```
periods=["DAY", "MONTH", "YEAR"]
```

Was later stored as an list:

```
periods = ( \
  "YEAR", \
  "MONTH", \
  "DAY", \
  "HOUR", \
)
```

Turns out that my code modifications are not needed. It is sufficient to keep configuration input properly formatted. Keep in mind that `()` allows to bring an collections. The [prefix syntax described](/2015/02/apache-felix-configuration-admin-with-array-values/) in earlier post, which determine type of contained elements, should still apply!

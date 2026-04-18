---
author: splatch
category:
  - inne
date: "2006-06-16T19:59:24+00:00"
guid: http://blog.dywicki.pl/2006/06/16/zgadnij-jaki-to-jezyk/
title: Zgadnij jaki to język
url: /2006/06/16/zgadnij-jaki-to-jezyk/

---
Kilka listingów. W czym to jest napisane?

```
list = [1: "a", 2: "b", 3: "C", 6: "asdf"]

list.each() {
key, value | println "${key} = ${value}"
}
```

```
add = { x -> { y -> x + y }}
println add(2)(4)
```

```
def power(n) {
{ base -> base**n }
}

square = power(2)
cube = power(3)

a = square(4)
println a // => 16
```

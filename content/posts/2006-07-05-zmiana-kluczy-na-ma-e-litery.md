---
author: splatch
category:
  - php
date: "2006-07-05T19:22:49+00:00"
guid: http://blog.dywicki.pl/2006/07/05/zmiana-kluczy-na-male-litery/
title: Zmiana kluczy, na małe litery
url: /2006/07/05/zmiana-kluczy-na-male-litery/

---
Bez żadnej pętli. :) Czy ktoś zna krótszy zapis nie korzystający z zewnętrznych funkcji?
```php
<?php

$target = array_combine(array_map('strtolower', array_keys($target)), array_values($target));

?>
```

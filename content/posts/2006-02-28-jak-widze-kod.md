---
author: splatch
category:
  - java
  - php
  - uml
date: "2006-02-28T00:56:03+00:00"
guid: http://blog2.dywicki.pl/2006/02/28/jak-widzie-kod/
title: Jak widzę kod?
url: /2006/02/28/jak-widzie-kod/

---
Tak pod wpływem natchnienia publikuję przykład diagramu klas reprezentujący kompozycje (zawieranie).
Diagram wykonany w programie Visual Paradigm. Komentarz usunięty ponieważ zachodził na klasy. :) ![diagram klas](http://img.dywicki.pl/kompozycja.jpg)
Kod (PHP):

```
<php class Jablon {     /**      *@var Jablko owoce znajdujace sie na drzewie      **/     protected $owoce = array(); } class Jablko {     /**      *@var mixed smak owocu      **/     protected $smak; } >
```

Związek jest określony - jedna instancja klasy Jablon może zawierać wiele owowców, ale może tych owoców nie być wcale. Zapis można sprecyzować: Jablon: 1..n, Jablko 1 oznacza, że jedna instancja klasy Jablko moze znajdowac się tylko w jednym obiekcie Jablon.
Jeśli się mylę, proszę o sprostowanie. Za jakiś czas kod w wersji dla Javy. ;)

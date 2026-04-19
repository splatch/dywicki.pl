---
author: splatch
category:
  - wiadomości
date: "2006-07-03T16:47:52+00:00"
guid: http://blog.dywicki.pl/2006/07/03/pobieranie-roznych-wartosci-z-tablicy-bez-petli/
title: Pobieranie różnych wartości z tablicy bez pętli
url: /2006/07/03/pobieranie-roznych-wartosci-z-tablicy-bez-petli/

---
Zwykle, kiedy potrzebujemy zmienną ilość wartości, które są w tablicy używamy pętli. Da się jednak obejść. Dzisiejszego dnia trafiłem na zadanie, które polegało na pobraniu fragmentu tablicy. Tablica była wierszem z bazy danych, który był wynikiem złączenia kilku tabel a ja chciałem pobrać wartości tylko dla określonej tabeli. Informacje o tym, jakie klucze mnie interesują miałem zapisane w obiektach metadanych, potrzebowałem tylko odpowiednio okroić wiersz do potrzebnych mi rozmiarów.

Problem rozwiązałem przy użyciu 4 funkcji, w tym 3 funkcji wbudowanych w PHP. Potrzebne były [array\_uintersect\_uassoc](http://pl.php.net/array_uintersect_uassoc), [array\_flip](http://pl.php.net/array_flip), [strcasecmp](http://pl.php.net/strcasecmp). Dodatkowo musiałem zdefiniować 1 funkcje, która dla 2 argumentów zawsze zwraca zero. Musi ona być zgodna z wymaganiami stawianymi przez "comparatory". Zero musi być zwracane by nie porównywać wartości (wg porównywaczy zero oznacza, że obie wartości są sobie równe).

Linia, która pobiera określone klucze:

```php
<?php
array_uintersect_uassoc($bigArray, array_flip($keysArray), 'alwaysReturnZero', 'strcasecmp');
?>
```

---
author: splatch
category:
  - db
  - inne
  - php
date: "2006-09-22T22:38:27+00:00"
guid: http://blog.dywicki.pl/2006/09/23/propel-12-przyspieszamy/
title: Propel 1.2 - przyśpieszamy
url: /2006/09/23/propel-12-przyspieszamy/

---
Zachęcony [komentarzem](/2006/09/21/propel-12-przyspiesz-go-nawet-do-2-razy/#comment-1094) do [poprzedniego posta](/2006/09/21/propel-12-przyspiesz-go-nawet-do-2-razy) postanowiłem zoptymalizować cały builder dla Propela. Zasada działania jest taka sama jak wcześniej - usunięcie zbędnych iteracji. Kod generowany przez moje poprawki nie należy do najszybszych, ale z [moich testów](http://www.phpfi.com/155990) wynika jednoznacznie - jest szybszy.
Szybki sposób instalacji [FasterPHP5ComplexPeerBuilder.php](http://delta.dywicki.pl/propel/FasterPHP5ComplexPeerBuilder.php):
pobrać plik http://delta.dywicki.pl/propel/FasterPHP5ComplexPeerBuilder.php
skopiować do folderu propel/engine/builder/om/php5.
w build.properties dla projektu ustawić dyrektywę:
```
propel.builder.peer.class = propel.engine.builder.om.php5.FasterPHP5ComplexPeerBuilder
```
Wskazuje ona na nazwę klasy której obiekt będzie odpowiedzialny za wygenerowanie kodu dla klasy tabeli (\*Peer).
Po tym wszystkim odpalamy generator z targetem om poleceniem:
```
propel-gen katlog-projektu om
```
bądź
```
phing -Dproject=nazwa -Dtarget=om
```.

Pamiętaj, [zmienić można](http://propel.phpdb.org/trac/browser/trunk/generator/default.properties#L195) o wiele więcej!

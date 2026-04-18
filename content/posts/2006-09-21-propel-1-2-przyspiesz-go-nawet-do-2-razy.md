---
author: splatch
category:
  - db
  - inne
  - php
  - wiadomości
date: "2006-09-21T18:16:02+00:00"
guid: http://blog.dywicki.pl/2006/09/21/propel-12-przyspiesz-go-nawet-do-2-razy/
title: Propel 1.2 - przyśpiesz go nawet do 2 razy!
url: /2006/09/21/propel-12-przyspiesz-go-nawet-do-2-razy/

---
Jak wiadomo szybkość nie jest domeną Propela. Dzisiejszego popołudnia na oficjalnym kanale Propela odbyłem rozmowę z osobą która twierdziła, że można przyśpieszyć propela o 3 razy (a przymajmniej metodę doSelectJoinAll). Nie zdziwcie się - miała ona rację! :)

Problem w doSelectJoinAll polega na tym, że są wykonywane zbędne iteracje mające na celu sprawdzenie czy element zawiera obiekt dołączanej encji. Można je z powodzeniem zastąpić odpowiednią mapą, która zawiera identyfikatory tych encji, które już są dodane do obiektu. Osoba, która to twierdziła miała odpowiedni kod, który zgadnijcie - zadziałał.
Aby zamiana była uniwersalna - zmieniłem co trzeba w generatorze. Sama metoda działa dwa i pół raza szybciej! Dla zainteresowanych - plik zmieniony przeze mnie - [PHP5ComplexPeerBuilder.php](http://delta.dywicki.pl/propel/PHP5ComplexPeerBuilder.php). Sprawdź czy Twoje doSelectJoinAll przyśpieszy. :)

Obecnie czasy, które uzyskuje:
0.045847177505493s - nowy build
0.06196403503418s - stary build
Nie mniej wcześniej różnice były znaczne - stary build potrafił zająć do 0.12 s! Wynik prosty - po zmianach (czasy mają znacznie mniejsze wahania) można przyśpieszyć tylko tą jedną metodę o dwa razy. Różnica będzie rosła wraz z ilością kluczy obcych w jednej tabeli. Im ich więcej tym stary build będzie wolniejszy.

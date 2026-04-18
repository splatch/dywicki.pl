---
author: splatch
category:
  - framework
  - java
  - testy-jednostkowe
date: "2008-09-02T16:51:50+00:00"
guid: http://blog.dywicki.pl/?p=129
summary: |-
  [![Okładka książki](http://blog.dywicki.pl/wp-content/uploads/2008/09/junit-209x300.jpg)](http://blog.dywicki.pl/wp-content/uploads/2008/09/junit.jpg)
  Temat testów jednostkowych nie pojawiał się na tym blogu tak często jak [PHP](http://blog.dywicki.pl/category/php) czy [JAXB](http://blog.dywicki.pl/category/jaxb), jakkolwiek temat ten poruszałem w 2 notach - [o testach](http://blog.dywicki.pl/2007/04/22/testy-jednostkowe/) oraz [o singletonie](http://blog.dywicki.pl/2007/02/01/singleton/).

  Tych, którzy chcieliby dowiedzieć się więcej o testach na przykładzie [JUnit](http://junit.org) i Javy zapraszam się do zapoznania z bardzo dobrą pozycją na temat testów jednostkowych, z którą miałem przyjemność się zetknąć.
title: JUnit. Pragmatyczne testy jednostkowe w Javie
url: /2008/09/02/junit-pragmatyczne-testy-jednostkowe-w-javie/

---
[![Okładka książki](/wp-content/uploads/2008/09/junit-209x300.jpg)](/wp-content/uploads/2008/09/junit.jpg)
Temat testów jednostkowych nie pojawiał się na tym blogu tak często jak [PHP](/category/php) czy [JAXB](/category/jaxb), jakkolwiek temat ten poruszałem w 2 notach - [o testach](/2007/04/22/testy-jednostkowe/) oraz [o singletonie](/2007/02/01/singleton/).

Tych, którzy chcieliby dowiedzieć się więcej o testach na przykładzie [JUnit](http://junit.org) i Javy zapraszam się do zapoznania z bardzo dobrą pozycją na temat testów jednostkowych, z którą miałem przyjemność się zetknąć.

[JUnit. Pragmatyczne testy jednostkowe w Javie](http://helion.pl/ksiazki/junit.htm) to tłumaczenie książki [Pragmatic Unit Testing in Java](http://www.pragmaticprogrammer.com/starter_kit/utj/index.html) z serii [The Pragmatic Programmers](http://www.pragmaticprogrammer.com/bookshelf/index.html). Nie wspominam tu o tej książce jak i całej serii przypadkowo, ponieważ pragmatyzm jest główną domeną tego bloga.

Tytuł ten traktuje o rzeczy bardzo ważnej, jaką są bez wątpienia testy jednostkowe. Nie jest to sucha referencja opisująca możliwości [JUnit](http://junit.org) ale przewodnik i poradnik - czyli nie tylko o tym czym testować ale po co i w jaki sposób to robić. Sam język w którymś momencie schodzi na drugi plan a ważna staje się na prawdę idea i szczytny cel, jakim jest wykluczanie błędów przed wdrożeniem aplikacji, tak by nie musiał się o nich w ogóle dowiadywać klient.

W książce znajduje się wiele przykładów i rozważań, pisanych lekkim językiem, całość czyta się lekko i przyjemnie. Jest to bodajże najczęściej wypożyczana pozycja z mojej domowej biblioteczki. W każdej kolejnej pracy (a troszkę ich w międzyczasie było :)) staram się komuś polecić tą pozycję i odzew jest zawsze pozytywny, co dowodzi, że praca autora włożona w tytuł była bardzo przemyślana. Z najważniejszych elementów, które zostają w książce omówione należy wymienić definiowanie warunków brzegowych, wykorzystanie "obiektów imitacji"
 (ang. _mock objects_) z użyciem biblioteki [Easy Mock](http://easymock.org/) oraz projektowanie kodu przyjaznego dla testów. Chyba wszyscy się ze mną zgodzą że są to elementy nieodzowne podczas tworzenia testów.

Na zakończenie przytoczę dwa akapity które najbardziej zapamiętałem z całej pozycji.

_Gdyby architekci sprawdzali mosty w taki sam sposób jak większość programistów to na świeżo wybudowanym moście środkiem jezdni przejeżdżałby jeden malutki samochód w ciepły dzień, bez jakiegokolwiek wiatru i innych zakłóceń atmosferycznych._ (o warunkach brzegowych)

_Z testami jednostkowymi jest jak ze strzyżeniem trawnika. Jeśli jest on dobrze utrzymany to jego uporządkowanie nie jest zadaniem trudnym. Jeśli jednak nasz trawnik zarośnie (w projekcie nie będzie testów jednostkowych) i pojawią się chwasty (błędy) to jego uporządkowanie (wprowadzenie testów) będzie zadaniem bardzo czasochłonnym._ (o filozofii testowania)

Koniec końców, siejąc pragmatyczną propagandę na rzecz testów :), zachęcam wszystkich do zapoznania się z opisywaną książką. Dla praktyków zawiera ona kilka trafnych spostrzeżeń a dla laików będzie znakomitym sposobem na wdrożenie się w temat testów.

Szkic tej noty czekał na publikację ponad rok, stąd może być on troszkę nieskładny.

---
author: splatch
category:
  - php
  - wiadomości
date: "2006-09-10T20:22:54+00:00"
guid: http://blog.dywicki.pl/2006/09/10/propel-20-co-nowego/
title: Propel 2.0, co nowego
url: /2006/09/10/propel-20-co-nowego/

---
Od jakiegoś czasu [trwają prace](http://propel.phpdb.org/trac/milestone/2.0) nad drugą wersją propela. Do najważniejszych zmian należy przejście na PDO. Jest to fakt, który najbardziej mnie smuci. Koszt zwiazany z wykorzystaniem Creole był zbyt wysoki jak na ORM i autorzy przeszli na znacznie szybsze PDO. W tej chwili dla każdej bazy danych jest stworzony odpowiedni, prosty adapter. Ilość obsługiwanych baz danych nie wzrośnie. W dalszym ciągu będzie to MySQL, PostgreSQL, Oracle, Microsoft SQL Server (Sybase), SQLite.
Niewątpliwą nowością jest cache, który będzie prawdopodobnie funkcjonował jako kolejny adapter. Mam nadzieję, że dodanie cache przyśpieszy nieco Propela, ponieważ część obiektów, mimo, że nie jest zmieniana (jest to fragment złączenia) zawsze jest pobierana i transformowana.
Spore zmiany zajdą w Criterii. W drugiej wersji budowanie złożonych warunków [stanie się banalne](http://propel.phpdb.org/trac/wiki/Development/Criteria), a to za sprawą całkiem nowej implementacji. Doczekamy się w końcu obsługi [idenity map](http://www.martinfowler.com/eaaCatalog/identityMap.html), dzięki czemu klucz główny zawsze będzie wskazywał ten sam obiekt.

[Więcej informacji](http://propel.phpdb.org/trac/wiki/Development/Roadmap) dla zainteresowanych nową wersją do znalezienia na wiki propela, na które serdecznie zapraszam. :)

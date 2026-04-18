---
author: splatch
category:
  - java
  - ogólne
  - php
date: "2007-04-03T22:01:47+00:00"
guid: http://blog.dywicki.pl/2007/04/04/o-abstrakcji-klas-i-interfejsow/
summary: |-
  Od jakiegoś czasu na [forum.php.pl](http://forum.php.pl/) spotykam się z różnymi zdaniami na temat interfejsów i klas abstrakcyjnych. Argumenty, które czasami się trafiają są chybione.

  Zacznijmy od tego, że trzeba potrafić rozróżnić interfejs od klasy abstrakcyjnej, **to nie to samo**!
  Interfejs jest najwyższym poziomem abstrakcji, który definiuje nowy, wolny od implementacji typ. Bez jakiejkolwiek linii kodu, tylko sygnatury metod publicznych.
  Klasa abstrakcyjna jest już początkiem konkretnej implementacji, zawiera kod ogólny i wymusza w klasach dziedziczących dorzucenie konkretnych metod, które są specyficzne, inne, różne. Mogą trafić się takie przypadki, że klasa abstrakcyjna zawiera 5 metod, a jej pochodne tylko jedną. Czy jest to uzasadnione? Oczywiście. Ta jedna metoda determinuje nowy typ, który jest jawną specjalizacją.
title: O abstrakcji klas i interfejsów
url: /2007/04/04/o-abstrakcji-klas-i-interfejsow/

---
Od jakiegoś czasu na [forum.php.pl](http://forum.php.pl/) spotykam się z różnymi zdaniami na temat interfejsów i klas abstrakcyjnych. Argumenty, które czasami się trafiają są chybione.

Zacznijmy od tego, że trzeba potrafić rozróżnić interfejs od klasy abstrakcyjnej, **to nie to samo**!
Interfejs jest najwyższym poziomem abstrakcji, który definiuje nowy, wolny od implementacji typ. Bez jakiejkolwiek linii kodu, tylko sygnatury metod publicznych.
Klasa abstrakcyjna jest już początkiem konkretnej implementacji, zawiera kod ogólny i wymusza w klasach dziedziczących dorzucenie konkretnych metod, które są specyficzne, inne, różne. Mogą trafić się takie przypadki, że klasa abstrakcyjna zawiera 5 metod, a jej pochodne tylko jedną. Czy jest to uzasadnione? Oczywiście. Ta jedna metoda determinuje nowy typ, który jest jawną specjalizacją.

Warto pamiętać, że w chwili gdy mamy klasę abstrakcyjną i kilka jej pochodnych dorzucenie interfejsu jest polisą na życie. Spójrzmy, na praktykę - co się powinno dziać w chwili, gdy pojawiają się kolejne pochodne oraz klasa abstrakcyjna rozrasta się do niebagatelnych rozmiarów? Wyodrębniamy wspólny interfejs (patrz [extract interface](http://refactoring.com/catalog/extractInterface.html)) i kolejne klasy abstrakcyjne.
Bez problemu dorzucamy kolejny typ i lecimy bez przestojów. W chwili gdy uzależnimy się od klasy abstrakcyjnej z jakimkolwiek kodem z biegiem czasu możemy mieć do czynienia z klasami, które zawierają po 10, 20 i więcej metod. Nawigacja po tak rozbudowanym typie jest uciążliwa a zrozumienie kodu wymaga przebrnięcia przez stos metod, które tworzą swoisty labirynt.
Rozsądne użycie interfejsów i klas pozwala nam tworzyć złożone ale przejrzyste struktury. Separacja abstrakcji od implementacji określana mianem wzorca projektowego, nazwa [mostem](http://www.google.pl/search?hl=pl&q=bridge+design+pattern&lr=&btnG=Szukaj+w+Google) (ang. _bridge_).

Budowa złożonej aplikacji bez użycia interfejsów jest posunięciem wysoce nierozsądnym, ponieważ to właśnie dzięki nim, nawet w językach kompilowanych ze statycznym typowaniem jesteśmy w stanie zyskać nadzwyczajnie elastyczny kod. W aplikacji, która jest rozwijana od dłuższego czasu (powiedzmy, ponad pół roku) to właśnie interfejsy są miejscami zawierającymi definicję poszczególnych warstw. To one powinny być trzonem każdej aplikacji (pomijając warstwę obiektów domenowych, gdzie ich rola nie musi być dominująca). Nawet jeśli koncepcja implementacji obróci się o 180 stopni to nasz interfejs, będący swoistą fasadą, ukryje te zmiany przed osobami, których one nie dotyczą bądź nie interesują.

Dorzucę tutaj przykład pewnego mechanizmu, który jakiś czas temu projektowałem i implementowałem. Jest to uproszczony schemat. Singletonu chciałem uniknąć, ale przesłanki ku jego zastosowaniu były na tyle duże, że pozostał. Duże **I** na początku nazwy oznacza interfejs, w nawiasach, dużymi literami wzorce.
```
(zdarzenie) (Mediator) (odbiorca)
ProgressEvent ---> IProgressMonitor <--- IProgressListener
 (generuj zdarzenie) ^ ^
 IDownloader ----------+ |
 IMerger | (Composite) | (Singleton)
 IProgressMonitorGroup < - - - SynchronizeManager

 ISynchronizeRequest
 ^
 | (Strategy)
 AbstractSynchronizeRequest
 +--------------+--------------+
 | | |
 | TableSynchronizeRequest |
ClassSynchronizeRequest ModuleSynchronizeRequest
```

Panowie, jak byście zaimplementowali te mechanizmy bez interfejsów? Jedną, wielką protezą?

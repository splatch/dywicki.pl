---
author: splatch
category:
  - java
  - ogólne
  - php
date: "2007-02-01T00:35:33+00:00"
guid: http://blog.dywicki.pl/2007/02/01/singleton/
title: Singleton
url: /2007/02/01/singleton/

---
Singleton jest chyba pierwszym z "wzorców projektowych" jaki wszyscy poznaliśmy. Prosty w implementacji, jeszcze łatwiejszy w użyciu, ale pociągający za sobą stos negatywnych konsekwencji.

W poszukiwaniu informacji i zdań o singletonie w polskim internecie trafiłem na [Wikipedię](http://pl.wikipedia.org/wiki/Wzorzec_singletonu), gdzie znalazłem zdanie, które podsumowało to czym jest tenże "wzorzec":

> Singleton jest też uznawany za [antywzorzec](http://pl.wikipedia.org/wiki/Antywzorzec "Antywzorzec"), gdyż często jest tylko eufemizmem dla [zmiennej globalnej](http://pl.wikipedia.org/wiki/Zmienna_globalna "Zmienna globalna").

W książce " [Refaktoryzacja do wzorców projektowych](http://helion.pl/ksiazki/refawp.htm)" padają kolejne dwa ważne zdania:

> Singleton to przede wszystkim wykorzystanie mechanizmów chroniących języka programowania do ochrony tylko jednego aspektu: jednostkowości.
> Prawdziwym problemem z singletonami polega na tym, że są doskonałą wymówką, aby lekko potraktować kwestię widoczności obiektów.

Główną przesłanką do stosowania singletonu jest to by mieć tylko jedną instancję danej klasy, ale czy nie da się załatwić tego w inny sposób? Oczywiście, że da się. Problemem jest tylko to by poświęcić więcej czasu na projekt, po prostu pomyśleć. W chwili gdy mamy połączenie z bazą danych i tak jest ono tworzona w jakimś miejscu, zwykle jest to jakiś kontroler bądź inny przyczółek w którym koncentruje się "władza". Jest to miejsce w którym i tak musimy daną instancję skonfigurować. Co to znaczy? Ano to, że cały ten singleton i tak nie jest autonomiczny, ponieważ jest zależny od czynników zewnętrznych. Idąc dalej - czy nie lepiej by było pchnąć stworzony już obiekt do tych miejsc gdzie będzie potrzebny?
Oczywiście, ktoś powie, że wydłużą się definicje metod i tak dalej. Fakt, to może być problem, ponieważ metody, które przyjmują powyżej 4 argumentów stają się "ciężkie". Ciężkie również pod względem zapamiętania ich wywoływania. Nie można tu również zapominać o tym, że nie musi być przymusu przekazywania za każdym razem danej instancji. Mamy do dyspozycji konstruktory jak również kompozycję, ogólnie pojętą fazę "konfiguracji". Zaletą unikania singletonów jest bardzo duży plus - obiekt nie jest widoczny tam gdzie nie powinien. Jest to znacznie lepsze podejście tym bardziej, że globalnie dostępny obiekt lubi kusić, kusić łatwością dostępu. Zawsze gdy myślisz o tym by użyć singletonu myśl o nim jak o zmiennej globalnej, bo tym w gruncie rzeczy się wkrótce stanie, elementem, który będzie powodował, że kod stanie się nieczytelny a kontrola dostępu do instancji wbrew pozorom, zamiast trudniejsza, jeszcze łatwiejsza.

### Wydajność

Za singletonem może przemawiać to, że powoduje on wzrost wydajności poprzez oszczędność pamięci (mniej stworzonych obiektów, mniej zużytej pamięci). Niestety często pomijanym faktem jest to, że zysk z takiego singletonu i tak jest niezauważalny, a w samym PHP wywołania statyczne są znacznie wolniejsze od tradycyjnych "dynamicznych" metod.

### Singleton a testy jednostkowe

Singleton znacznie utrudnia testowanie kodu, ponieważ uniemożliwia on pełne rozbicie komponentów. Elastyczniejsze okazuje się użycie metod, które pozwalają "wstrzyknąć" określony obiekt, który jest w danej chwili potrzebny. Elementy, które są przykryte singletonem są niewymienne, muszą być stałe. W chwili gdy singleton tworzy sam instancję klasy pochodnej zaczynamy mieć do czynienia w gruncie rzeczy z abstract factory  (fabryką abstrakcyjną) bądź _factory method_ (metodą fabryczną) co w efekcie można nazwać bardzo prosto - przerostem formy nad treścią.
Raz stworzony i zaszyty wewnątrz singletonu obiekt jest świętą krową, nie da się go zastąpić mimo, że na potrzeby testów potrzebny jest leciutki obiekt, który tylko dostarczy odpowiednie dane bądź zachowa informacje o wywołaniach (stub, mock object). Co gorsza, w chwili gdy mamy do czynienia z typowym "kontekstem", który tworzy w prywatnym konstruktorze kolejne usługi nie mamy możliwości ich podmiany na czas testów. Osobiście spotkałem się z sytuacją, w której testy kończyły się niepowodzeniem, ponieważ w konstruktorze kontekstu było tworzone okno aplikacji.

### Połączenie singleton+singleton a Java

Warto zapamiętać, że odwołania pomiędzy singletonami w Javie nie mogą występować w fazie inicjowania obiektów. To znaczy że kod, w którym konstruktor singletonu A wymaga singletonu B, a ten z kolei singletonu A to dostaniemy odpowiedni wyjątek. Przykładowo: kontekst tworzy nowe połączenie, które pobiera instancję jeszcze niestworzonego kontekstu by dobrać się do konfiguracji.

### Wnioski

Zanim zdecydujesz się na użycie singletonu przeczytaj trzy cytowane zdania i zastanów się czy rzeczywiście nie ma alternatywy? Nie ma sytuacji w której singleton jest niezbędny, zawsze można znaleźć coś lepszego niż zmienna globalna do przekazywania instancji. Pamiętaj singleton jest wypaczeniem idei programowania obiektowego na potrzeby leniwych programistów.

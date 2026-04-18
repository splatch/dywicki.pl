---
author: splatch
category:
  - php
date: "2005-11-03T11:37:07+00:00"
guid: http://blog2.dywicki.pl/2005/11/03/zend-framework-i-php6/
title: Zend Framework, PHP5, PHP6
url: /2005/11/03/zend-framework-i-php6/

---
Wczoraj podczas rozmowy z Chmolu dowiedziałem się, że podobno są źródła nowego frameworka w CVSie. Nie czekając pobrałem najnowszą wersję (oznaczoną numerkiem 6)

Troszkę to trwało, zanim pobrały się te wszystkie biblioteki, ale w końcu są.. Rozglądam się po źródłach (teoretycznie wersji 6), ale nic specjalnego tam nie ma, jakiś przykład napisany w C do filtrowania globalsów (moduł main?!), który i tak w praktyce na nic się nie zda, bo kto da możliwość dodawania własnych rozszeżeń na serwie... ;), w Zend Engine również zmian brak, nic specjalnego nie ma poza bałaganem. Zdaje się, że poza pracami nad wsparciem unicode nic się tam nie dzieje. Owszem są branche do namespaces, ale nic się nie zmienia w samych plikach. Zmiany w zend\_objects.h kończą się 3 sierpnia. Rozglądałem się za jakąś wersją pośredniczącą (5.5), ale takiej gałęzi w CVSie nie ma. Po przejrzeniu ChangeLoga stwierdziłem, że tam nic o PHP6 nie ma, jedyne co się pojawia to PHP 5.1. Dodam, że postępowałem zgodnie z instrukcjami na PHP.net. No, ale nic znalazłem moduł php6, zobaczymy co w nim jest.

Powiem, że poczułem zawód, ale nie tylko przez to, że nie znalazłem źródeł nowego frameworka. Mam wrażenie, że prace nad nowszymi wersjami (pięć i sześć) są opóźniane przez to, że wciąż jest prowadzony support dla czwórki. Przecież piątka była wydana przeszło rok temu, a tutaj cały czas wlecze się 4. PHP.net dostało cięgi za wydanie wersji 4.4, ale powinno coraz bardziej dostawać w kość za wydawanie kolejnych fiksów do 4.4<. Jak ma wejść piątka na stałe, skoro cały czas są poprawiane stare błędy. IMO gdyby pojawiły się jakieś krytyczne bugi, które nie zostałyby usunięte w 4 ale w 5 tak to część hostów zdecydowałaby się na przejście z średniowiecza do renesansu :). Możliwe, że się mylę w tym ostatnim zdaniu, bo nie znam do końca faktów, ale IMO przyjdzie nam jeszcze sporo poczekać za PHP6 jak i PCP, tak długo, że Mojavi 4 z pewnością zdąży wyjść i kolejna wersja tego dobrego frameworka będzie pisana pod PHP 6/PCP.

Przy okazji spójrzcie jakie cuda można zobaczyć z MySQL:  
[http://splatch.php.org.pl/\_img/baza.jpg](http://splatch.php.org.pl/_img/baza.jpg)

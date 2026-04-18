---
author: splatch
category:
  - ogólne
  - testy-jednostkowe
date: "2007-04-21T22:34:15+00:00"
guid: http://blog.dywicki.pl/2007/04/22/testy-jednostkowe/
summary: |-
  ## Praktyka

  W tym miejscu bazuję na swoim bądź co bądź skromnym doświadczeniu, które nabyłem pracując w [AGI](http://autoguard.pl). Była to pierwsza firma, w której spotkałem się z wykorzystaniem testów jednostkowych. Pamiętam do dzisiaj walki o 70% pokrycie kodu testami. :).
  Nie mniej, nie robiliśmy tego tylko po to by zobaczyć zielone słupki w raporcie wygenerowanym przez [PHP Unit](http://phpunit.de). Takie pokrycie kodu testami gwarantuje znaczne ograniczenie błędów wychodzących z czasem, głównie dlatego, że znajduje się już podczas pisania testów.
title: Testy jednostkowe
url: /2007/04/22/testy-jednostkowe/

---
## Praktyka

W tym miejscu bazuję na swoim bądź co bądź skromnym doświadczeniu, które nabyłem pracując w [AGI](http://autoguard.pl). Była to pierwsza firma, w której spotkałem się z wykorzystaniem testów jednostkowych. Pamiętam do dzisiaj walki o 70% pokrycie kodu testami. :).
Nie mniej, nie robiliśmy tego tylko po to by zobaczyć zielone słupki w raporcie wygenerowanym przez [PHP Unit](http://phpunit.de). Takie pokrycie kodu testami gwarantuje znaczne ograniczenie błędów wychodzących z czasem, głównie dlatego, że znajduje się już podczas pisania testów.

Zasadniczo możemy wyróżnić dwa podejścia do testów - bardzo inwazyjne - projektowanie sterowane testami (ang. _Test Driven Development_), czyli opieranie się w całości na testach. Wygląda to mniej więcej w ten sposób, że tworzymy interfejs, do niego test i testujemy przy użyciu [mock objects](http://www.mockobjects.com/) (w wolnym przekładzie można to traktować jako obiekty pozorne). Jednym z założeń jest to, że testy nie mogą się zmieniać.
Drugie podejście, z którego korzystaliśmy to, w sumie nienazwane, wspomaganie testami. W skrócie, pisząc klasę piszemy również test do niej, tak by na bieżąco sprawdzać poprawność działania kodu. Jeśli zmieni się interfejs etc, testy również mogą się zmienić.

## Korzyści

### Nie pozwól błędom zawładnąć Twoją aplikacją

Tak jak wcześniej wspominałem korzyści, które niosą ze sobą testy jednostkowe to wyłapywanie błędów nim mają szansę coś popsuć. Ile razy szukałeś błędu, który był zakopany tak niewiarygodnie głęboko zakopany, że jego lokalizacja zajęła Ci kilka dni? Ile razy po prostu zrezygnowałeś z walki stosując jakąś brudną protezę? Testy, które sprawdzają niewielkie fragmenty kodu (po prostu pojedyncze metody) lokalizują błędy z dużą dokładnością. Szukanie błędu gdy wiadomo gdzie on tkwi jest znacznie łatwiejsze i mniej czasochłonne. Zwykle to lokalizacja miejsca, gdzie błąd powstaje zajmuje więcej czasu niż jego naprawienie. Jedna metoda, która generuje błędny wynik prowadzi do błędnej implementacji kolejnej, ta z kolei determinuje błąd w innym algorytmie.

### Łatwiejszy refaktoring i wprowadzanie zmian w kodzie

Refaktoryzacja ma to do siebie, że źle przeprowadzona, a co gorsza nie sprawdzona potrafi zrodzić spore zamieszanie. Testy jednostkowe pozwalają sprawdzić czy zmiany, które wprowadziliśmy w celu usprawnienia kodu nie niosą ze sobą błędów.
Często zmiany, które wprowadzamy nie mogą być nazwane refaktoryzacją, po prostu dodajemy spory fragment kodu, który może ingerować już w istniejącym. Wybitnym przykładem mogą być tu aspekty (najprościej można to określić jako modyfikowanie zachowania kodu z zewnątrz). Innym po prostu poszerzenie implementacji jakiejś klasy pomocniczej o jedną metodę i na prawdę drobna zmiana w dwóch warunkach istniejącej. Czy masz pewność, że Twój kod działa? Zazwyczaj tak, bo testujesz go "manualnie", ale czy wiesz, jaki wpływ mają Twoje zmiany na inne fragmenty aplikacji? Być może zmieniona metoda generowała w pewnych okolicznościach błędny wynik, który stał się początkiem wspomnianej lawiny?

### Praca w grupie

Testy są szczególnie ważne gdy nad kodem pracuje cały zespół zapanowanie nad całością przedsięwzięcia wymaga wielkiego zaangażowania. Można je ograniczyć wprowadzając konieczność sprawdzania kodu pod kontem jego spójności z pozostałymi fragmentami aplikacji (akapit wyżej). To, że kod działa u Ciebie, nie znaczy, że zadziała u kogoś innego. Są różne platformy sprzętowe, jeszcze różniejsze konfiguracje środowisk. Czy jesteś w stanie zagwarantować, że kod bazujący na jakiejś strukturze katalogów zadziała? Czy zastanawiałeś się jak zareaguje aplikacja gdy jest uruchomiona ze złą konfiguracją? Zapewnij sobie neutralne środowisko testowym, nieskażone niczyim IDE by być pewnym, że to zadziała też u klienta jak i prezesa.

### Automatyzacja

Automatyzacja całego procesu jest ważna. Nikt nie będzie siedział non stop i sprawdzał co godzina czy testy są ok bądź się wywalają. Nikt nie ma na to czasu i pieniędzy. Kierownik projektu ma na głowie więcej rzeczy niż tylko testy a programiści "robią swoje". Nikt nie chce uruchamiać testów, niech zrobi to zatem maszyna. Maszyna, na której będzie stało oddzielne, gołe środowisko specjalnie pod testy. Automat pobierze aktualną wersję kodu i uruchomi testy do niego. Może robić to co godzinę bądź co dwie, ale najlepiej by robił to po każdej zmianie w repozytorium. Programista to zwierz leniwy i zdarza mu się zapomnieć o uruchomieniu testów. Przypomnienie mu o nich przez automat będzie milsze niż reprymenda dwa tygodnie po tym, jak aplikacja przestała działać u wszystkich i znaleziono winnego (vide kozła ofiarnego).

### Redukcja kosztów

Jak to określił [Ross](http://agileweb.org), testy to jeden ze sposobów na wyprzedzenie kosztów, swoistego rodzaju zapłata dzierżawy. Tracimy czas (pojęcie względne) inwestując w pisanie testów, ale zyskujemy z jego biegiem, ponieważ kod naszej aplikacji po prostu działa. Nie musi nad nim czuwać sztab testerów, którzy klikają i zaznaczają w formularzach "to nie działa" z opisem typu "pole 'nazwa firmy' jest ignorowane przy próbie zmiany jego wartości". Wskazanie miejsc, gdzie rodzi się błąd nie wydaje się trudne. Albo niepodpięte mapowanie do pola, albo źle wygenerowane zapytanie. Banał. Ale spytaj kogoś, kto powie od razu który fragment kodu to robi? Sytuacja nie jest najgorsza, wykryli ją testerzy, ale bywają gorsze, znacznie gorsze. **To, że kod się kompiluje nie znaczy, że działa**.

## Studium pewnego błędu

Załóżmy, że aplikacja działa od 3 miesięcy w środowisku produkcyjnym, u klienta. Nagle okazuje się, że to samo pole nie może zawierać apostrofów, ponieważ powstaje błąd zapytania SQL. Inna sytuacja - dorzucenie & w nazwie powoduje błąd w wyświetlaniu strony. Wpisanie specyficznej wartości w formularzu powoduje pokazanie się białej strony. Esteci się cieszą, ale klienci już nie. Padają gromy ze strony klienta. Obrywają wszyscy od góry do dołu. Warto poświęcić czas na to by mieć komfortowe warunki pracy, z minimalną ilością negatywnych emocji i tarć na linii Ty - Twój zwierzchnik.
Wyeliminowanie błędu pojawiającego się u klienta jest drogie a koszt jest wprost proporcjonalny do ilości klientów. Na błędach tracą wszyscy. Firma traci pieniądze, ponieważ programista musi zlokalizować i naprawić błąd. Programista traci werwę. Nikt z nas nie lubi babrać się w czyimś kodzie, nie wiadomo jaki by on nie był, w poszukiwaniu jakiejś niedoróbki. Jednak najgorsza może być **strata wizerunku**. Na rynku jest pełno firm. Czy może poszczycić się Twoja? Wszyscy kochają jakość i wszyscy się nią chwalą, dopóki nie dojdzie do konfrontacji z rzeczywistością. Zadbaj by Twój produkt był dostarczony z minimalną ilością błędów, a możesz liczyć na to, że klient wróci i być może pociągnie za sobą swoich partnerów biznesowych.

### Zaproszenie do dyskusji

Na końcu pragnę zachęcić wszystkich do zadawania pytań jak i dzielenia się swoimi doświadczeniami. Jakie są Twoje doświadczenia z testami jednostkowymi? W jaki sposób przebiegało ich wdrożenie? Czy jesteś sceptycznie nastawiony? Może jesteś przeciwko? To tylko część z tematów, które możemy poruszyć.

---
author: splatch
category:
  - 7-grzechów
  - php
  - php6
  - wiadomości
date: "2006-07-15T18:37:49+00:00"
guid: http://blog.dywicki.pl/2006/07/15/7-grzechow-glownych-php-inwokacja/
title: 7 grzechów głównych PHP, inwokacja.
url: /2006/07/15/7-grzechow-glownych-php-inwokacja/

---
Postami w tej kategorii chcę pokazać jak dalekie PHP jest od ideału. Mam nadzieję, że większość z tego co piszę kiedyś zostanie poprawiona, nie mniej póki co, są to grzechy ciężkie, które pokazują słabości PHP.

### Zend

Zend jest firmą, która bez wątpienia ma największy wpływ na PHP. To Zend tworzy najważniejszy element PHP jakim jest Zend Engine.

To co mam do zarzucenia Zendowi to nieumiejętność wykorzystania swojej pozycji. Nie potrafi on wykorzystać swojej pozycji by ugrać coś na rzecz PHP. Być może dlatego, że jako firma jest zbyt mały by cokolwiek znaczyć. Od jakiegoś czasu Zend powoli produkuje papkę marketingową, którą wciska, że PHP jest enterprise podczas gdy samemu PHP jest do tego bardzo daleko. To, że został zmieniony silnik obsługujący obiekty, upodobniono składnię do Javy, wydano nową (piątą) wersję PHP nie czyni go enterprise.

Kiedy PHP stanie się językiem Enterprise? Kiedy dostarczy stosownych narzędzi i technologii. Niestety w chwili obecnej nie ma ani jednego ani drugiego. PHP jest wciąż językiem, który ma do tego aspiracje, ale nie ma możliwości (zupełnie tak jak Zend). Mam nadzieję, że z biegiem czasu i not z tej kategorii będę to udowadniał.

Z czym się do tej pory kojarzy PHP? Ze "stronkami" produkowanymi przez gimnazjalistów i licealistów. Nie odbiega to od faktów. Pytanie, dlaczego Zend zmierza w kierunku większych klientów? Odpowiedź jest oczywista, większe pieniądze. Tylko, za przeproszeniem, z czym do ludu?!

**API, Technologie**

Brak jakiegokolwiek API dla rozszerzeń. Parsery XML to nie tylko SAX, nie tylko DOM. Jeśli ktoś chce zaimplementować cały standard samodzielnie czy też go rozszerzać (choćby w samym PHP!) powinna być taka możliwość, niestety do tej pory jej nie ma i prawdopodobnie nie będzie, ponieważ PHP nie jest porządnie zaprojektowane.
Inną sprawą jest to, że nikt się tym nie zajmował i nie zajmuje. Błędne koło się zamyka, nikt nie myśli nad nowymi, porządnymi rozwiązaniami oraz technologiami opartymi o ten język, ponieważ PHP nie jest gotowe na taki krok. Wystarczy spojrzeć na [Javę](http://jcp.org)... Dla przykładu podam platformę J2EE. Sun publikuje pełną specyfikację i dostarcza przy okazji własny serwer, który w pełni tą specyfikację obsługuje. Nie przeszkadza to by równocześnie na rynku były obecne inne podmioty takie jak JBoss, BEA Web Logic i podstawa wszystkiego - Apache Foundation z Tomcat\`em.

**Społeczność i Zend Framework**

Jak wiadomo społeczność PHP nie jest mała. Względem innych języków można powiedzieć, że jest ogromna. Nie mniej problemem społeczności jest jej podział i brak konsolidacji. Zend nie sprzyja tworzeniu społeczności wokół PHP. Przykładem może być Sun (który stoi na podobnej pozycji z Javą). Czym najłatwiej zgromadzić społeczność wokół własnej platformy? Oczywiście - projektami. Im więcej projektów tym więcej zaangażowanych ludzi i osób oraz firm nimi zainteresowanych. Tymczasem, co robi Zend? Prze ślepo w jednym kierunku, mianowicie w stronę własnego Frameworka, który od ideału jest daleki.

Jeden framework nie zdobędzie serc wszystkich programistów tym bardziej, że w PHP korzystanie z gotowych rozwiązań do w dalszym ciągu rzadkość. Zamiast wspierać chociażby konkurencyjne rozwiązania Zend nie robi nic, nawet nie kiwa palcem by przyciągnąć do siebie projekty. Przykładem może być Cake PHP, wokół którego zgromadziła się pokaźna grupka osób oraz Prado, które jest rozwiązaniem zgoła innym, działającym na zupełnie innych zasadach. Czyżby Zend nie wiedział, że w mnogości tkwi siła? Najwyraźniej tak, nie wspierając społeczności PHP wiele traci. To działa w obie strony, tak samo jak Zend może liczyć na takie samo wsparcie społeczności jako ono na jego.
**PDO**

PDO jest rozwiązaniem zdecydowanie spóźnionym i niedostatecznym jeśli myślimy o poważnych zastosowaniach. Podczas, gdy na rynku rozwijały się rozwiązania takie jak AdoDB i jego klony Zend nic nie robił w celu zatrzymania tego procesu. Doszło do sytuacji, kiedy PHP obsługuje wiele baz ale przez skrajnie różne rozszerzenia i funkcje. Jeszcze do niedawna było poprawiane rozszerzenie do obsługi PostgreSQL, by przypominało chociaż pozostałe. Do niedawna korzystanie chociażby z dwóch różnych baz danych wymuszało korzystanie z paskudnie napisanego AdoDB bądź implementowania tego samodzielnie.

PDO miało to zmienić, ale niestety nie do końca. PHP5 nie jest jeszcze zainstalowane na wielu serwerach, nie wspominając PHP 5.1 od którego PDO zostało wprowadzone. Wcześniejsze rozwiązania bazujące na "tradycyjnych" implementacjach używających funkcji natywnych nie mogą przeskoczyć nagle na PDO a nie mówiąc już o brakach w PDO.
Nie obsługuje ono wszystkich baz jakie do tej pory obsługiwało PHP oraz nie uwalnia definitywnie od różnic pomiędzy bazami. Pod tym względem PDO może przebić chociażby Creole nie mówiąc już o AdoDB, które obsługuje praktycznie wszystkie bazy, jakie do tej pory w PHP się pojawiły.

### PHP6

Zbyt małe zmiany. Tak jak pisałem wcześniej, to jest powolna ewolucja, podczas gdy PHP jest potrzebna przemiana. Jeśli PHP ma z brzydkiego kaczątka kiedykolwiek stać się łabędziem to w tym tempie jeszcze lata ciężkiej i rzemieślniczej pracy przed nami, tym bardziej, że jesteśmy jeszcze w jajku, z którego się nie wykluliśmy.

Czy dodanie przestrzeni nazw diametralnie zmieni PHP? Zmieni zapewne ale nie na tyle by instytucje takie jak banki zainwestowały w PHP, bo jak wcześniej pisałem, nie jest to powód sam z siebie. Muszą się pojawić narzędzia, które wspomagają, uproszczają, porządkują prace nad bardzo dużymi aplikacjami. Ciągłe luki, niedoróbki, wydania, które są jednodniowe z racji krytycznych błędów bezpieczeństwa. To wszystko nie sprzyja budowaniu pozytywnego wizerunku samego PHP.

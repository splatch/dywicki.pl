---
author: splatch
category:
  - php
  - php6
  - wiadomości
date: "2005-12-20T00:38:07+00:00"
guid: http://blog2.dywicki.pl/2005/12/20/php6-bo-mialy-byc-wielkie-zmiany/
summary: Oto co zrozumiałem z [http://www.php.net/~derick/meeting-notes.html](http://www.php.net/~derick/meeting-notes.html). Nie jest to tłumaczenie a moja interpretacja, a jako, że znam angielski gorzej niż źle błędów może być bardzo wiele. W sumie bardzo nawet bym tego chciał, bo z tego co przeczytałem zmian jest na prawde mniej niż można by było się spodziewać.
title: PHP6 bo miały być wielkie zmiany ...
url: /2005/12/20/php6-bo-mialy-byc-wielkie-zmiany/

---
Oto co zrozumiałem z [http://www.php.net/~derick/meeting-notes.html](http://www.php.net/~derick/meeting-notes.html). Nie jest to tłumaczenie a moja interpretacja, a jako, że znam angielski gorzej niż źle błędów może być bardzo wiele. W sumie bardzo nawet bym tego chciał, bo z tego co przeczytałem zmian jest na prawde mniej niż można by było się spodziewać.

Unicode on/off
Wprowadzenie obsługi unicode spowolni niektóre funkcje do ciągów o 300% a całe aplikacje nawet o 25%. Stąd logiczne wydaje się przesunięcie konfiguracji unicode do php.ini tak by providerzy mogli dynamicznie włączać i wyłączać to wsparcie. Dodatkowo wszystkie rozszerzenia muszą zostać teraz oparte na UTF, a te, które go nie wspierają będą musiały konwertować ciągi do oczekiwanego formatu.

Optymalizacja \[\]
Obecnie używanie operatora \[\] do pobierania kolejnych znaków ciągu jest bardzo wolne (rzutowanie ciągu do tablicy = wrzucanie kolejnych znaków do tablicy i zwracanie spod określonego indeksu), wymusza na PHP skanowanie ciągu od początku.

Usuwanie funkcjonalności
register\_globals = zmienne globalne są źródłem wielu problemów związanych z bezpieczeństwem aplikacji. Wyrzucenie tej opcji będzie najlepszym sposobem na pozbycie się ich. W końcu register\_globals miało zniknąć już w PHP5, ale została tylko domyślnie wyłączona. Nareszcie jakiś stanowczy krok..
magic\_quotes = automatyczne dodawanie do ciągów slashy również prowadziło do wielu problemów. Dokumentacja jest wystarczającym uzasadnieniem. Usunięcie tej opcji ma zmotywować programistów do używania rozszerzenia input\_filter.
safe\_mode = na serwerach unikowych pojawiał się problem z uprawnieniami, ponieważ najczęściej PHP jest uruchamiane z uprawnieniami użytkownika nobody. Stąd posunięcie związane z wyłączeniem kontrolowania identyfikatora grupy/użytkownika i pozostawienie tylko dyrektywy open\_basedir, która powinna być wystarczająca do kontrolowania dostępu do katalogów i plików.

Var w klasach.
W PHP 5 użycie słowa kluczowego var powodowało wyświetlenie błędu przy raportowaniu na poziomie E\_STRICT. W PHP6 var będzie aliasem dla słowa kluczowego public i nie będzie powodowało wyświetlania błędu.
Referencje przy tworzeniu obiektów będą powodowały wyświetlenie ostrzeżenia przy raportowaniu E\_STRICT.

W PHP6 zabraknie również wsparcia dla ZE1 (dyrektywa zend.ze\_compatibility zostanie usunięta), ponieważ była to tylko metoda by zachować kompatybilność wstecz do PHP4. Próba uruchomienia PHP6 z włączoną dyrektywą ze\_compatibility spowoduje błąd z poziomu E\_CORE\_ERROR.

Usunięcie wsparcia dla GD1 i Freetype 1 jest podyktowane tym, że są to biblioteki dla których są już rozwinięte nowsze wersje.

dl() – funkcja ta była przyczyną wielu problemów. Od tej pory funkcja dl będzie tylko dostępna w CLI.

CGI/FastCGI mode
FastCGi jest znacznie szybsze od CGI i dlatego teraz domyślnie jeśli PHP będzie korzystać z interfejsu CGI będzie nim FastCGI.

Dynamiczne klasy – definiowanie klas w warunkach - if() { class … } else { class … } spowalnia silnik i utrudnia kompilowanie klas.

register\_long\_arrays, HTTP\_\*\_VARS – podobnie jak ze wsparciem dla ZE1 – opcja usunięta. Przy próbie uruchomienia z włączoną dyrektywą zostanie wyrzucony błąd.

Stare nazewnictwo konstruktorów – jest ono problemem, ponieważ powoduje konflikty, gdy w klasie istnieją metody o nazwie konstruktora klasy dziedziczącej:
`       class A {
function B() { // zostanie wywołane przy new B!!!
}
}
class B {
} `

$b = new B();
?>
Spowoduje to wywołanie metody A::B(). Nie mniej postanowiliśmy pozostawić tą opcję.

Małe literki – będziemy dążyć do tego by standaryzować nazewnictwo funkcji – by były one zapisywane małymi literami lecz nie usuniemy tego problemu w PHP6.

break $var;
W rzeczywistości ta opcja nie działa i nie ma ku temu wyraźnych przesłanek. Usuwamy wsparcie dla dynamicznego break.

PECL
Domyślnie w PHP6 będzie w kompilowany XMLReader oparty na SAX (Simple Api for XML) oraz XMLWriter.
Przeniesienie rozszerzeń do obsługi bazy danych (nie PDO) do PECL. Od wprowadzenia w PHP 5.1 nie potrzebujemy już rozszerzeń do obsługi baz danych. Część rozszerzeń zostanie zastąpiona przez PDO, a te których możliwości nie można było w nim zaimplementować (MySQLi, OCI8) zostaną przeniesione.
Przeniesienie ereg do PECL. Od teraz wyrażenia POSIX zostaną domyślnie wyłączone. Nie będzie też możliwości wyłączenia PCRE.
Decyzje o przesunięciu innych rozszerzeń do PECL zostanie podjęta po ich przejrzeniu.
Naprawa ext/soap oraz dodanie wsparcia dla wsse/secext – rozszerzenie ext/soap będzie domyślnie włączone (w PHP5 najpierw trzeba było je ściągać, dopiero potem pojawiło się jako biblioteka).
Dopuścimy również możliwość usunięcia otwartego pliku. Pod unixem nie jest to problem – wystarczy użyć unlink, niestety pod windows broni używanych plików przed usunięciem (w obawie o utratę stabilności? ;)).

Dodatki w silniku
64o bitowy integer.
Dodanie goto. Hm… chyba komuś za bardzo zależy na zbliżeniu PHP do Javy.
`for ($i = 0; $i         if (true) {
break blah;
}
echo "not shown";
blah:
echo "iteration $i\n";
}
?>`
Zastąpienie isset($foo) ? $foo : ‘bar’ poprzez operator ?:.
Planujemy również użyć ifsetor przy nowym rozszerzeniu input\_filter:
`$blahblah = input_filter_get(GET, 'foo', FL_INT) ?: 42;
?>`
Opuszczamy środkową wartość w ?: i odrzucamy propozycję z ifsetor().

Dopuszczenie w foreach przeglądania tablic wielowymiarowych. Od tej pory konstrukcje typu…
`$a = array(array(1, 2), array(3, 4));
foreach( $a as $k => list($a, $b)) {
}
?>`
…będą obsługiwane. Dopuszczony zostanie również foreach bez as – foreach($var $key => $val).

W PHP6 zostanie rozwiązany problem z operatorami {} i \[\] przy stringach.
Obsługa {} zostanie usunięta, a obsługa \[\] rozszerzona o dodatkowe możliwości:
Np. będą możliwe konstrukcje jak w pytonie \[2,4\] \[2,\] \[,-2\] \[-3,2\]

Operator @ jest bardzo wolny. Obecnie Ilia and Marcus pracują nad rozwiązaniem tego problemu.

Parametry nazwane – nie wprowadzimy ich. (jednak nie wszystko z pytona się podoba ;))

Ustalenie kolejności argumentów dla wszystkich funkcji.
Jako, że tak diametralna zmiana pociągnie za sobą wiele problemów z istniejącymi aplikacjami postanowiliśmy nie robić tego.

Zmiany w OO.
Wymagam wywołania mojego konstruktora!!
W PHP6 będzie możliwość ustawienia czy wywołanie konstruktora jest ważne. Jeśli tak klasa dziedzicząca będzie zmuszona do wykonania parent::\_\_construct();
Dopuszczenie w interfejsach definiowania konstruktorów. W chwili obecnej nie ma takiej możliwości.
Nie widzieliśmy sprzeciwów do umieszczenia tej możliwości, dlatego w PHP6 się pojawi.

Obecnie podpowiadanie typów dla metod dziedziczonych nie jest zachowywane. Stąd mogą powstawać spore rozbieżności w interfejsach różnych klas konkretnych. Jako, że nie jest to sprawa najwyższej wagi na razie rezygnujemy ze sprawdzania tego.

Rzutowanie obiektów.
Stwierdziliśmy, ze nie ma sensu dodawać kolejnych magicznych metod które będą wykonywane przy rzutowaniu – pozostaje jedna \_\_toString().

Przestrzenie nazw.
Jak wiadomo obecnie w PHP nie ma przestrzeni nazw. Alternatywą mogą stać się moduły (kod w oryginalnym dokumencie). Nie wiadomo jednak jak przestrzenie w końcu będą wyglądać.

Używanie nie zadeklarowanego pola spowoduje błąd. Zwiększy to niezawodność kodu.

Podpowiadanie typów:
Nie będzie podpowiadania typów dla pól/własności – to jest nie po PHPowemu.
Będzie podpowiadanie typów dla wartości zwracanych, musimy tylko

Wywoływanie metod.
Próba wywołania metody dynamicznej jako statycznej jak dynamicznej spowoduje błąd – E\_FATAL. Wywoływanie metod statycznych jako dynamicznych będzie dalej dopuszczalne.

Delegatorzy.
PHP nie obsługuje delegatorów, i nie da możliwości ich implementacji.

Zostanie usunięte wsparcie dla znaczników <%. W dalszym ciągu będzie dostępny znacznik <?, zostanie dodany również <?php =.

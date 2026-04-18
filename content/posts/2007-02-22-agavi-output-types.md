---
author: splatch
category:
  - agavi
  - framework
  - mvc
  - php
date: "2007-02-22T20:41:37+00:00"
guid: http://blog.dywicki.pl/2007/02/22/agavi-output-types/
title: Agavi, Output types
url: /2007/02/22/agavi-output-types/

---
Jedną z nowości jaką niesie Agavi w wersji > 0.10 jest mechanizm output types. Jest to bardzo proste rozwiązanie, które umożliwia uniknięcie gimnastyki z tworzeniem widoków w różnych technologiach, z którymi wiąże się różna logika. Banalny przykład. Te same dane prezentujemy w postaci HTML jak i PDF a do tego możemy je pobierać przez XmlHttpRequest. Dane są praktycznie identyczne, różny jest format wynikowy i proces jego tworzenia. Dla zwykłej strony wskazujemy szablon, dorzucamy dane i koniec, dla XmlHttp zwracamy JSONa. Stworzenie outputu w formacie PDF nie będzie tak proste jak pozostałych, ponieważ konieczne będzie stworzenie układu strony, dorzucenie fontów etc. Ogólnie w żaden sposób nie da się połączyć tych formatów w jednym widoku bez sporej ilości warunków i "protez". By uniknąć zakopania się w tym wszystkim zwykle tworzy się dodatkową akcję, która w sporej części pokrywała się z pierwotną a różni się tylko widokiem i szablonami. Począwszy od Agavi 0.11 problem przestaje istnieć.

W pliku konfiguracyjnym [output\_types.xml](http://trac.agavi.org/browser/trunk/samples/app/config/output_types.xml) określamy [renderer](http://trac.agavi.org/browser/trunk/src/config/xsd/output_types.xsd#L31) dla danej technologii, dodajemy obiekty przedefiniowane (parameters name="assigns") i następnie konfigurujemy mapowania adresów do plików (swoją drogą najlepsza implementacja tego mechanizmu z jaką się do tej pory spotkałem). Jest [odpowiedni plik](http://trac.agavi.org/browser/trunk/samples/app/config/routing.xml) zawierający [definicję routingu](http://trac.agavi.org/browser/trunk/src/config/xsd/routing.xsd).

Zajmijmy się jednak w pierwszej kolejności konfiguracją output types:
```xml
<?xml version="1.0" encoding="UTF-8"?>
rreqctlusr
 tmsubdir
 text/html; charset=UTF-8

 text/javascript; charset=UTF-8
```

Jak widać trochę tej konfiguracji jest, nie mniej celowo usunąłem przekazywanie parametrów do drugiego formatu wynikowego by pokazać, że w najskromniejszej wersji definicja taka potrafi się zmieścić maksymalnie w 10 linijkach.
Do wszystkich parametrów możemy odwoływać się w widoku. Specyficzne są parametry o nazwie assigns oraz i18n. Pierwszy z nich Agavi wykorzysta po to by od razu w fazie tworzenia renderera wrzucić do niego wskazane obiekty, które znajdują się w kontekście. Drugi z atrybutów jest szczególnie przydatny podczas tworzenia aplikacji z wieloma językami. Parametr i18n jest prefiksem dla nazwy pliku. W przypadku gdy przypiszemy mu wartość subdir framework będzie szukał szablonów po katalogach np. **pl/IndexSuccess**, **en/IndexSuccess**. Inne, dopuszczalne wartości to postfix oraz prefix. Nazwy szablonów to odpowiednio **IndexSuccess\_pl** oraz **pl\_IndexSuccess**
Atrybut extension określa suffix dla nazwy pliku, którego użyje Agavi przy wczytywaniu szablonu np **IndexSuccess.tpl.php**.
Argumenty ignore\_slots oraz ignore\_decorators odnoszą się do strategii budowania widoku. W chwili gdy zrezygnujemy z nich wynik nie będzie dekorowany. Tzn. w odpowiedzi użytkownik zobaczy tylko treść wygenerowaną przez widok akcji.
**Tutaj drobna uwaga. W wersji stabilnej Agavi 0.11 mechanizm ten zachowuje się nieco inaczej. Wybór plików oraz zachowanie dekoratora determinuje layout i elementy layer.**

Następnie w routes.xml dla każdej ścieżki możemy określić również format wynikowy danych. Np:
```xml
```

W tym momencie, wszystkie adresy kończące się na **/ajax** będą obsługiwane w nieco inny sposób niż pozostałe. To znaczy, że nie będzie dekorowany output. Dodatkowo zostaną użyte inne pliki szablonów do generowania treści.

Zdaję sobie sprawę z tego, że opisałem ten mechanizm dość chaotycznie i bardziej skupiłem się na jego konfiguracji, ale na przykładową aplikację będziecie musieli poczekać do czasu gdy skończę artykuł dla PHP Solutions (czyżby Agavi tematem numeru?) bądź do pojawienia się nowego Code-House. :)

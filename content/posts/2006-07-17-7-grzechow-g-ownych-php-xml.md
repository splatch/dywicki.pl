---
author: splatch
category:
  - 7-grzechów
  - php
  - wiadomości
  - xml
date: "2006-07-17T22:21:10+00:00"
guid: http://blog.dywicki.pl/2006/07/18/7-grzechow-glownych-php-xml/
title: 7 grzechów głównych PHP, XML
url: /2006/07/18/7-grzechow-glownych-php-xml/

---
**Na początku odpowiedź na post, który napisał [Zyx](http://www.zyxist.com/pokaz.php/7_grzechow_php_komentarz).** _Aktualnie każdy, kto chce napisać nowe rozszerzenie do PHP, musi tylko znać język C, znać cel swej pracy oraz przeczytać rozdział 46 dokumentacji PHP zatytułowany "Zend API: Hacking the Core of PHP" i poświęcony właśnie tworzeniu modułów._

Zend API to nie wszystko. Moduły kompilowane nie są wyjściem super-uniwersalnym. Na co drugim serwerze nie ma opcji by dorzucić własne rozszerzenie. Wiele modułów z PECLa leży tam od lat, są one praktycznie nie rozwijane, także ich ilość niewiele może poświadczyć.

_Projekt ten nie miał nigdy poprawiać po egoistycznie nastawionych twórcach baz danych oraz ich kreatywnym podejściu do implementacji standardu ANSI SQL, ale na plus należy zaznaczyć, że niektóre braki bibliotek klienckich są emulowane (np. podpinanie)._

Nikt nie mówi, że PDO miałoby to poprawiać. Spójrz na JDBC, producenci baz samodzielnie dostarczają sterowników do połączeń i dbają o to by były zgodne z własną bazą oraz kolejnymi standardami w JDBC.

_Ale głupotą byłoby negowanie go tylko dlatego, że w kwestiach wielkoskalowych ma braki, a Zend ostatnio stosuje dziwne podejście w kwestii doboru priorytetów i debugowania swego sztandarowego projektu._

Diabeł tkwi w szczegółach. Brak stosownych narzędzi i rozwiniętych rozwiązań technologicznych jest sporym problemem, który znacznie utrudnia tworzenie aplikacji.

**XML i SAX**
SAX jest świetnym standardem. Niestety, ustawianie gromady opcji dla parsera to jakaś porażka. Do tego domyślne zmienianie nazw tagów na wielkie litery.. jak by ktoś z tego korzystał.
SAX mógłby być świetnym rozwiązaniem, gdyby istniała klasa domyślnego parsera, która wymuszałaby implementacje tylko 2 metod - startElement i endElement. Niestety, jesteśmy skazani na to co, jest w PEAR, czyli kod pisany pod PHP 4.
Poza tym problem z SAXem w PHP jest taki, że jeśli ktoś chce korzystać z przestrzeni nazw w dokumencie, którego używa ma niewielkie szanse na to, że cokolwiek uda mu się ugrać, ponieważ parser nie przekazuje do handlera informacji o tym do jakiej przestrzeni nazw należy tag. Dla przykładu w Javie metoda startElement wygląda w następujący sposób:

void startElement(string uri, string localname, string qname, attributes atts)
a w PHP
void startElementImpl(resource parser, string localname, array atts)
czyli zupełnie tak samo jak by tych przestrzeni nie było. Jedyny sposób to wyciąganie prefiksu w nazwie (bo otrzymujemy np foo:bar) i sprawdzanie względem wcześniej zdefiniowanych poprzez xmlns.
**DOM**
Za implementację DOM Panom z Zenda należą się gratulacje, przede wszystkim dlatego, że w końcu jest to moduł doprowadzony do standardu, zgodnie z zaleceniami W3C. Nie będę już narzekał, że jest to DOM Level 2 a nie 3 co by nie było, że się za wiele czepiam ;). Świetnie, że DOM jest częścią Core PHP. Nie ma problemów z tym, czy DOM jest czy nie.

Testując DOM przed napisaniem tej noty trafiłem na błąd w obsłudze encji. Działają one w atrybutach ale nie w treści. Sprawa wygląda w ten sposób, że encje predefiniowane takie jak "e; są widoczne, zatem gdzie są moje? Uciekły bo się przestraszyły tagów.

**SimpleXML**
SimpleXML jest bramką pomiędzy DOM a SAX. Oferuje mniejszą funkcjonalność niż DOM, ale wymaga o wiele mniej pracy niż SAX. Po prostu, wczytujesz plik i działa. Niestety, nie jest tak różowo. W nim również znalazłem niedoróbkę. Nie jestem pewien czy nie została ona załatana, problem polegał na gubieniu atrybutu przy kolekcji. Denerwuje mnie również to, że nikną mi informacje o przestrzeni nazw. Jeśli przeglądam dokument iteracyjnie tracę informację o tym z jakiej przestrzeni jest element.

**XSLT**
Zbyt małe możliwości połączenia PHP i samego procesu transformacji. Do XSLT można przekazać tylko ciągi, zatem nie ma mowy o tym by wrzucić tablicę i na jej podstawie wykonać jakąś iterację. Niestety, nie te czasy. Plusem jest to, że można tworzyć funkcje do których parser potrafi się odwołać.. niestety są to albo funkcje albo statyczne odwołania do klas.

**XSL-FO**
XSL-FO to standard, który umożliwia przekształcenie dowolnego dokumentu XML do postaci binarnej takiej jak PDF. Niestety w PHP nie doczekaliśmy się jeszcze żadnej implementacji tego standardu, poza jedną klasą w PEAR, która usiłuje wykonać transformację przy użyciu Javy.

**XSD**
Z XSD w PHP jest zasadniczy problem. Nie istnieje. Można sprawdzić czy dokument jest zgodny ze schematem korzystając chociażby z DOMa, niestety nie ma żadnych narzędzi, które umożliwiają tworzenie klas ze schematu. Skala problemu znacznie się powiększa w chwili gdy korzystamy z WSDL, które pośrednio korzysta z XSD. Nie ma opcji, żeby stworzyć jakikolwiek typ złożony w PHP bez własnego kodu.

Rozbudowa o przykłady, być może wkrótce.

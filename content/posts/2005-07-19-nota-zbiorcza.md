---
author: splatch
category:
  - ogólne
date: "2005-07-19T14:55:59+00:00"
guid: http://blog2.dywicki.pl/2005/07/19/nota-zbiorcza/
title: Nota zbiorcza
url: /2005/07/19/nota-zbiorcza/

---
Witam po sporej przerwie :).

Piszę to o 23:23, tuż po ustawieniu budzika na 6:20, wiadomo - kto rano wstaje temu Pan Bóg daje. Wstałem dzisiaj po 5 i jak przystało na rasowego programistę czytałem w autobusie "Thinking in Java" w wydaniu trzecim. Po kilku dniach spędzonych z tą książką czuję się zagubiony. Przykładów jest sporo, a nie miałem okazji ich nawet uruchomić. Jak na razie nie miałem trudów z przyswojeniem wiadomości, okaże się jak zrozumiałem treść kiedy zacznę robić ćwiczenia. Tak czy siak tak łatwo się nie poddam. :)

Na polsacie właśnie leci jakieś badziewie pod postacią VIP. Normalnie nie wiem skąd oni biorą takie denne seriale.

Co z Thorem? No jak na razie po pracy jestem zbyt wypompowany żeby wyłuskać z siebie cokolwiek entuzjazmu do kodowania. W międzyczasie przeczytałem rozdział o projektowaniu aplikacji z TiJ. Na podstawie porad, które tam wyczytałem postaram się przeanalizować budowę Thora raz jeszcze. No zadanie będę miał nieco utrudnione dlatego, że to nie jest 'zwykła' aplikacja OOP tylko framework nastawiony na wykorzystanie komponentów. Po przerobieniu parsera na kształt tego z Prado mam problemy z połączeniem całości w logiczny zespół, to znaczy może inaczej - nie wiem w którym miejscu tworzyć obiekty komponentów na podstawie uzyskanej z parsera tablicy, która ma kształt drzewa. To znaczy:

 `Tree[korzeń][numer] = array(type,attr,childs);` gdzie korzeń to kolejne komponenty występujace w dokumencie:

```
<!-- tutaj nagłówki meta itp. --> <thor:panel> 	treść 1 </thor:panel> <thor:panel> 	treść 2 </thor:panel>
```

 tablica Tree będzie w tym przypadku wyglądać tak: `Tree[0][0] = array(panel); Tree[1][0] = array(panel);`

W przypadku gdy komponent posiada dzieci są one dodawane poprzez referencję pod kluczem childs. Po var\_dump-ie widać ładne drzewko.

Aby wyświetlić całe drzewo wystarczy zacząć od Tree\[n\]\['childs'\]

Podczas prac miałem na prawdę spore problemy z uzyskaniem tego wyniku. Najpierw nie mogłem stworzyć odpowiedniej referencji następnie były problemy z tagiem <prop:atrybut></prop:atrybut>. Teraz kolejnym problemem jest uzyskiwanie wnętrza komponentu. Gdy wewnątrz komponentu znajduje się inny uzyskany kod jest znacznie okrojony, a dokładniej pozbawiony znajdujących się wewnątrz niego dzieci. Będę musiał zajrzeć do Prado i podejrzeć jak to jest rozwiązane. :)

Wracają do lektur, które przerabiam - po TiJ czeka na mnie "Java. Aplikacje bazodanowe, najlepsze rozwiązania" oraz "Java. Programowanie sieciowe". Wszystkie książki pożyczyłem od kolegów z pracy. Dodatkowo obok mnie siedzi osoba zajmująca się Javą więc w razie problemów mam do kogo się zwracać.

Obecnie na pierwszy plan przed Thora wychodzi LiveChat, który piszę. Jest to poniekąd typowy chat, lecz nie mogę zdradzić póki co większej ilości informacji ponieważ ktoś może mnie uprzedzić z rozwiazaniem i będę na przegranej pozycji :).

W pracy jestem zmuszony do pracy na Auroksie którego zaczynam powoli mieć dość. Raz - problemy z Xami, dwa ogólna niechęć do tej dystrybucji. Zaczynam się coraz bardziej zastanawiać nad Slackware'm.

Jeśli już robię notkę zbiorczą to wspomnę, że zmartwychwstało OPB (Open Powe Board). Miejmy nadzieję, że czasy anarchii już nie wrócą. Mam do siebie spory żal za to, że zaniedbuję Pomocnik XUL, na prawdę nie mam usprawiedliwienia, kurde leń ze mnie i tyle. :(

PS. Jedyną rzeczą na która mogę na prawdę narzekać w Bydgoszczy jest jedzenie, a właściwie jego częsty brak w odpowiedniej ilości i jakości...

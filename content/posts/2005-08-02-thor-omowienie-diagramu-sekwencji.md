---
author: splatch
category:
  - thor
date: "2005-08-02T09:33:38+00:00"
guid: http://blog2.dywicki.pl/2005/08/02/thor-omowienie-diagramu-sekwencji/
title: Thor omówienie diagramu sekwencji
url: /2005/08/02/thor-omowienie-diagramu-sekwencji/

---
**Za jakość diagramu przepraszam :)**

![kliknij aby powiększyć](http://splatch.php.org.pl/_img/ds_m.jpg)Na przedstawionym diagramie widać 4 węzły. Pierwszy to użytkownik, który będzie wywoływał zdarzenia. Myślę, że jego zadań nie trzeba opisywać ;). Celowo operuję na ogólnikach aby nie zawężać Wam jak i sobie pola widzenia.

Pierwszy element dostarczany przez Thora to komponent. Każdy komponent posiada obiekt JavaScript, który komunikuje się z obserwatorem. Komponent przekazuje informacje o rodzaju zdarzenia. Jako parametr jest przekazywana zdefiniowana wcześniej wartość. Np. OnClick=”KliknietoMnie();” zostaje przekształcone na OnClick=IDentyfikator.call( onClick, KliknietoMnie()) powoduje to, że do obserwatora wędruje OnClick oraz KliknietoMnie.

Obiekt obserwatora ma za zadanie przekazać informację do serwera, którą zajmie się w dalszym ciągu framework. Obserwator jest bardzo ważnym elementem. Od jego działania zależy poprawność działania systemu. Obserwator musi rozróżnić operacje które ma zlecić do wykonania innym obiektom reprezentującym komponenty czy wysłać żądanie prosto do serwera.  

Nota: czy rozsądniejsze by nie było stworzenie rozbudowanego zaplecza po stronie JS i pozwolić operować komponentom na sobie, a informacje do serwera wysyłać tylko po to żeby np. zapamiętać pozycję komponentu typu 'sticky'?

Z pewnością AJAX będzie miał za zadanie zwracać treści z bazy danych. Myślę, że będzie się to odbywało poprzez zmianę atrybutu datasource. Zostanie o tym powiadomiony obserwator, który automatycznie wyślę rządanie do serwera. Ten odpowiednio sparsuje rządanie oraz określi czy użytkownik posiada uprawnienia do pobrania określonej treści. Ostatnim elementem w łańcuchu, lecz najważniejszą jest samo PHP. Thor będzie musiał na początku wygenerować stronę wraz z zestawem początkowych komponentów. Dobrze by było gdyby mógł później wydawać polecenia Obserwatorowi, który dalej kazałby usuwać komponenty bądź je dodawać.

Gdzieś trzeba jeszcze zostawić miejsce na style, to znaczy będzie musiała starczyć zwykła zmiana cssa, ale w dobie CSS 2 oraz 3 to nie powinno stanowić problemu (poza IE).

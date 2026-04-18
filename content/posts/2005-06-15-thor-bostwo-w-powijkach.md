---
author: splatch
category:
  - thor
date: "2005-06-15T21:27:54+00:00"
guid: http://blog2.dywicki.pl/2005/06/15/thor-bostwo-w-powijkach/
title: Thor, bóstwo w powijkach
url: /2005/06/15/thor-bostwo-w-powijkach/

---
Projekt, o którym wspomianłem wcześniej został nazwany. Inspiracją nazwy był Odin beli. Sięgnąłem do historii o wikingach i znalazłem tam inne bóstwo - Thora. Silniki swoich poprzednich aplikacji nazywałem imieniem Sachmet - egipskiego bóstwa wojny i płodności. Thor w mitologii nordyckiej jest bogiem burz błyskawic jak również rolnictwa. Thor patronował ognisku domowemu i małżeństwu. \[ [1](#pnote-15-1)\]

Projekt postaram się tworzyć przy wykorzystaniu tego, co przeczytałem w książce o wzorcach projektowych. Podstawą będzie ogólny schemat, który pozwoli przejść **od ogółu do szczegółu**. Na pewno postaram się przed napisaniem czegokolwiek stworzyć diagram UML. ![kliknij aby powiększyć](/images/Class_Diagram2_m.png)Zacząłem pisać, następnie stworzyłem diagram. Dyskutując o zapisanym rozwiązaniu znalazłem w nim nieścisłość. Obiekt LanguageContainter będzie zawierał poszczególne języki (może zajść potrzeba odwołania się do domyślnego języka - np. z angielskiego do polskiego). Całość oparta jest na statycznej tablicy languages. Przy próbie użycia języka trzeba się odwołać do kontenera - LanguageContainer::getLanguage('PL','Main') i następnie operować na zwróconym obiekcie. Gdzie zatem nieścisłość? Po wywołaniu wcześniejszego kodu kontener przy wywołaniu LanguageContainer::getLanguage('PL','User') zwróci obiekt, który utworzył wcześniej, ponieważ w tablicy languages jest już indeks PL. Rozwiązanie te dyskryminuje też nieco jego obszerność.

Wydaje się, że odwołanie Language::getMessage('Main','Section') jest prostsze i szybsze. Niestety utrudnia ono korzystanie z dwu języków podczas jednego żądania..

#### Notes

\[ [1](#rev-pnote-15-1)\] Na podstawie [http://pl.wikipedia.org/wiki/Thor](http://pl.wikipedia.org/wiki/Thor)

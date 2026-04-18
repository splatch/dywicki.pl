---
author: splatch
category:
  - ogólne
date: "2005-06-26T22:08:57+00:00"
guid: http://blog2.dywicki.pl/2005/06/26/przerwa/
title: Przerwa
url: /2005/06/26/przerwa/

---
Jako, że od tego poniedziałku rozpoczynam pracę nie będę mógł tyle czasu poświęcać Thorowi. Dodatkowym utrudnieniem będzie brak kompa po pracy. To nie koniec trudności. Po zciągnięciu komputera do Bydgoszczy (gdzie znalazłem pracę) będę miał problem z internetem. Ogólnie trudno mi powiedzieć coś więcej niż to, że mam zacząć prace. Poważnie zapowiada się hardokorowy tydzień, nie tylko ze względu na to, że będę wstawać rano. ;)  

Pierwsze wrażenia z biura pozytywne. Nie mogę się doczekać pierwszego zadania, słowem zżera mnie ciekawość... o tym czy Bydgoszcz zmieni w moich wizerunek z brzydkiego miasta na gorszą lub milszą mieścinę będę pisał... (za jakiś czas), o wrażeniach z pracy i o pierwszym zadaniu powiem tyle, ile będę mógł (test już miałem!).

Co do Thora. Totalnie zmieniła się budowa parsera. Obecny, jeszcze nie do końca sprawny przechowuje obiekty do samego wyświetlenia aplikacji, czyli zmiany wykonywane z poziomu aplikacji nie będą problemem, a nawet te z poziomu szablonu winny być przeprowadzane względnie łatwo. Stary parser zaraz po znalezieniu komponentu parsował go, dodatkowo nie miał możliwości zagnieżdżania komponentów wewnątrz siebie, co było bardzo poważną wadą. Długie godziny spędzone nad Prado jednak zaowocowały nowym parserem (swoją drogą zasada działania bardzo zbliżona). Mam nadzieję, że uda mi się zaaplikować cache do parsera, myślę też o tym, czy nie zapisywać 'względnie' skompilowanych szablonów. Przetrawię to w tym tygodniu.. co do buforowania obiektów - myślę, że starczy serializowanie obiektu na podstawie ID i zapisywanie w pliku...  

Garść koncepcji rzucę na pewno za jakiś czas, póki co przestudiuję raz jeszcze wzorce projektowe.

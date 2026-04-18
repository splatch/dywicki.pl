---
author: splatch
category:
  - ogólne
date: "2005-06-23T00:17:51+00:00"
guid: http://blog2.dywicki.pl/2005/06/23/thor-plany-i-rozterki/
title: Thor, plany i rozterki
url: /2005/06/23/thor-plany-i-rozterki/

---
Thor, szczegóły no.2

Z wpisów zamieszczanych tutaj można wywnioskować, że pracuję tylko nad Thorem. Nie zaprzeczam, że poświęcam mu dużo czasu, można powiedzieć, że aż za wiele.  

Chciałem rzucić garścią informacji – co dodałem.  

Zaaplikowane różne wersje językowe na poziomie wyjątków, czyli przy pojawieniu się jakiegoś problemu wystarczy użyć: `throw new Exception( LanguageContainer(‘Blad skladni’, ’JakisTamKominukat’) );`

Myślę nad dodaniem do komponentów źródeł danych, to znaczy – w kodzie dawałoby się tylko: `<thor:Komponent datasource=”tutaj_jakies_zrodlo” />` Dalsze pomysły związane z komponentami to możliwość ustawiania atrybutów HTML takich jak style, border, class aby maksymalnie uprościć ich budowę.

W fazie testów jest też coś na kształt magazynu zmiennych. Można powiedzieć, że jest to klasa, która ma za zadanie przy użyciu sesji utrzymywać dane, aby same komponenty wiedziały co z nimi się wcześniej stało. Ot, przykładowo czy panel ma być ukryty czy też widoczny.  

Wpadłem na niebanalny pomysł z CMSem – pomyślałem, ze skoro Thor staje się coraz bardziej komponentowy to czemu by nie zainwestować w coś na kształt panelu treści? Jedyne, co trzeba zrobić to określić skąd pobierać dane (tutaj miejsce dla źródeł danych) oraz typ klasy (news, artykuł itp.). Planuję użyć do uzyskania odpowiedniego efektu wzorca stratega, gdzie poszczególne klasy we własnym zakresie aplikują metodę zwracającą kod.  

Jeśli wszystko się pozytywnie rozwinie to za jakiś czas wrzucę jakąś prezentację.  

Chciałbym też wspomnieć, że znalazłem pracę.:)

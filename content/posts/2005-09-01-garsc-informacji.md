---
author: splatch
category:
  - php
date: "2005-09-01T11:34:40+00:00"
guid: http://blog2.dywicki.pl/2005/09/01/gar-informacji/
title: Garść informacji ;)
url: /2005/09/01/gar-informacji/

---
Planowałem to zrobić wcześniej, ale dopiero świadectwo Beli mnie ostatecznie zmotywowało :). Byłem pozytywnie zakoczony tym, że jeszcze ktoś mnie odwiedza...

![katalogi...](http://splatch.php.org.pl/_img/katalogi.gif) Obok prezentuję struktóre katalogów. Myślę, że jest ona dośćprzejrzysta, można powiedzieć zapożyczona z Mojavi.  

Troszkę może Was dziwić tak wielkie rozbicie konfiguracji, ale jest to zamierzone, moim zdaniem łatwiej się pracuje w takim rozdrobnieniu, ponieważ każdy moduł posiada oddzielnąkonfigurację i może mieć zgoła inne parametry. W kataloguWebApp znajdują się ustawienia globalne takie jak dostęp do bazy danych oraz zdefiniowane moduły + pluginy, czyli klasy które można zastąpić (rozwiązanie Beli z Odina).  
**WebApp/Config.php** zawiera definicje ścieżek.  
**WebApp/Modules/Default/config.xml** to definicja akcji, jakie znajdują się w module oraz obsługę błędów (404,403,500);  
**WebApp/Modules/Config/ActionName.xml** zawiera szczegółowe dane takie jak używane filtry i akcje odpalane w stosie (można to nazwać blokami). Zastanawiam się czy implementować coś takiego jak Bela – fallbacki, dla tych akcji które zwróciły fałsz.  

Gdyby trafiły się jakieś pytania odnośnie katalogów i ich zawartości piszcie.  

Teraz garść informacji o tym, jak całość ma być zorganizowana.  

Zgodnie z tym, co pisałem na forum.php.pl zaimplementuję łańcuchy akcji, czyli możliwość uruchamiania kilku akcji połączonych w logiczną całość.Będzie to przypominało kreatory na kształt tych, które są w Prado. Przykład takiego łańcucha dla dodawania nowego użytkownika:  
`Wpisz token -> Podaj dane -> Wpisz kod aktywacyjny` Nie ma możliwości odpalenia akcji “Podaj dane” poza stosem. Podobnie jak “kod aktywacyjny” jest dostępna tylko po przejściu wcześniejszego ogniwa. Zainteresowanych zapraszam do zapoznania się z tematem na forum.  

Na akcję składać będą się różne obiekty.  
**Action** – warstwa logincza  
**ActionForm** – część warstwy prezentacyjnej, interfejs XML2Form  
**ActionValidator** – filtr sprawdzający poprawność danych  
**ActionErrors** – lista błędów, które zostały wygenerowane poprzez wcześniejsze elementy  

Obiekt ActionErrors zawiera obiekty ActionError, które umożliwiają internacjonalizację na poziomie walidacji formularzy. ;)  

W temacie na forum wspominałem o beanach, ale nie pisałem jak planuję to rozwiązać. Do użytkownika będzie docierać tylko FormBean więc łatwo załatwić to poprzez dodanie hiddena z jego nazwą. Podczas, gdy na serwerze wciąż będzie kopia użytkownik będzie miał do dyspozycji swój egzemplarz, który będzie mógł modyfikować.  

Akcja będzie mogła samodzielnie sprawdzać dane (podobnie jak w Strutsie i Mojavi) poprzez metodę Action::validate(). Gdy metoda ta zwróci fałsz nie dojdzie do uruchomienia metody Action::run(). Zostanie wyświetlona poprzednia akcja razem z błędami, które powinny znajdować się już w obiekcie ActionErrors. Gdy walidacja przebiegnie pomyślnie zostanie uruchomiona warstwa logiczna. Jeśli metoda Action::run() zwróci fałsz zostanie cofnięta transakcja uruchomiona poprzez kontroler (ROLLBACK). Akcja może zawierać również metodę finalize, która jest uruchamiana tylko po pomyślnym zakończeniu tranzakcji (COMMIT). Jeśli akcja znajduje się w łańcuchu to metoda finlize nie jest wykorzystywana. Jest ona uruchamiana dopiero w ostatniej akcji, czyli jak we wcześniejszym przykładzie po poprawnym wpisaniu kodu aktywacyjnego.

**ActionForm**  

Jak wcześniej pisałem jest to interfejs do obsługi formularzy. Jeśli pojawią się błędy ActionForm powinien samodzielnie podświetlić pola i dodać komunikaty o błędach.  

W ustawieniach odnośnie formularzy można zdefiniować czy formularz może być wysłany kilkakrotnie (blokada post-backu). Jeśli istnieje potrzeba blokady ponownego wysłania danych to do formularza jest dodawany hidden z losowym tokenem, który równocześnie jest zapisywany w sesji.  

ActionForm to klasa uniwesalna, zawsze jest wykorzystywany tylko jeden egzemplarz/byt.

**XML2Form**  

Jest to bardzo ważny elementcałego systemu. Klasa XML2Form zawiera obiekty elementów, beany oraz obiekt klasy XML2FormLayout. Po dodaniu do obiektu XML2Form beana trafia on do odpowiedniego elementu, który powinien samodzielnie zmienić wartości swych pól.  

Przy wyświetlaniu XML2Form przekazuje referencje do Layoutu ($this), który kolejno wyświetla elementy. Element formularza ma podstawową metodę, która zwraca kod kontrolki, np. <input ..>, Opis jest wyciągany poprzez metodę Element::getDesc(), a sprawdzany poprzez Element::gasDesc().  

Teraz przykładowy szablon. ColumnRadioLayout:  

```
<table [class=”<?=$this->hasError()?'error_input':'normal_input'?>"]><tr><td> <?=$this->getLabel()?><br /> <?=$this->getDesc()?> </td><td> <?$this->toLayout()?><br /> <?$this->gerError()?><br /> </td></tr></table>
```

 Jeżeli w szablonie strony wcześniej pojawiło się wywołanie getErrors to przy elementach getError() nie zwraca tekstu, jest tylko podświetlone pole. Myślę, aby część tego zadania przerzucić na CSS i zmieniać tylko klasę.  

 Diagram nie zgadza się z opisem, ponieważ od pewnego czasu nie zajmowałem się XML2Form i rozmyła mi się wizja całości. Być może, uda mi się to lepiej zorganizować, póki co czekam na opinie. ![kliknij aby powiekszyc](http://splatch.php.org.pl/_img/XML2Form_m.jpg)

Internacjonalizacja załatwiona będzie niemal tradycyjnie, tzn. Formularz + zczytywaniez nagłowka ACCEPT\_LANGUAGE przy tworzeniu nowej sesji. W zależności od wybranego tłumaczenia jest wczytywany plik z komunikatami.

Tym sposobem straciłem 2h na pisanie tego i nie stworzyłem DTD do plików konfiguracyjnych..

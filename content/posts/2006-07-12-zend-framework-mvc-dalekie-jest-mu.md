---
author: splatch
category:
  - framework
  - mvc
  - php
date: "2006-07-12T21:23:41+00:00"
guid: http://blog.dywicki.pl/2006/07/12/zend-framework-mvc-dalekie-jest-mu/
title: Zend Framework-MVC dalekie jest mu
url: /2006/07/12/zend-framework-mvc-dalekie-jest-mu/

---
Zaprawdę, zaprawdę powiadam Wam drodzy czytelnicy Zend Framework do pełnej implementacji MVC ma jeszcze bardzo duży kwał drogi.

Dzisiejszego dnia postanowiłem poświęcić parę minut na bliższe spotkanie z ZF. Jak się szybko okazało nie był to czas spędzony bezowocnie. Utrwaliłem się w przekonaniu, że ZF to nie jest to czego szukam oraz znalazłem buga i to dość niewygodnego.. ;)

Dlaczego moje uprzedzenie do ZF nie zmalało a tylko wzrosło? Dlatego, że to co w sumie zobaczyłem odbiega od znanego mi (z innych frameworków) MVC. Może potraktuję Was tutaj odrobiną kodu:

```php
<?php

$view = new Zend_View();
$view->setScriptPath('./views/');
$view->a = 'asdf';
echo $view->render('example.php');
?>
```

Taki oto kawałek kodu jesteśmy smuszeni umieścić w kodzie akcji by uruchomić widok. Gdzie jest wygoda, automatyczne uruchamianie widoku? Nie wspomnę już o tym, że w ten sposób Zend znacząco ogranicza możliwości zmiany warstwy obsługującej widok.

Wzorowym rozwiązaniem dla mnie jest użycie Render'ów, które świetnie się spisują do tworzenia outputu. Nie czepiam się tutaj braku takich elementarnych rzeczy jak konfiruacja, której praktycznie nie ma. Być może ktoś stwierdzi, że można to sobie zaimplementować, ale przepraszam, po co biorę framework? Po to by jak najmniej implementować, by tworzyć logikę biznesową a nie rozszeżać framework.
Elementem, który został obsłużony w ZF niecodziennie są filtry. Są one traktowane jako pluginy. Może i dobrze, ale co oprócz filtrów może być wykonywane przed akcją?
Bardzo mnie boli brak konkretnego flowa, narzuconego z góry. Przyznam, że podoba mi się taki standard ponieważ przyzwyczaiłem się do tego korzystając z Mojavi i Symfony.
Komuś odpowiada swoboda? W kodzie nie ma miejsca na swobodę. Im więcej możliwych dróg tym więcej możliwych błędów. Babranie się w ścieżki i tak dalej, po co to komu? Po to by móc rozdzielać sobie aplikację na mniejsze "moduły" a te na jeszcze mniejsze akcje? Skoro tak to dlaczego Zend\_Controler\_Front nie działa w katalogu innym niż document root? Ano dlatego, że panowie, którzy pisali Zend\_Controler\_Router twarto stwierdzili, że ich adres jest stały i ma format /controller/action. A co w przypadku kiedy mam index.php/controller/action? Otóż moim kontrolerem nie będzie controller a index.php. Nasz adres ogranicza się tylko do REQUEST\_URI, a to czy SCRIPT\_NAME w nim się znajduje bądź jesteśmy w katalogu innym niż document root nie ma wpływu na działanie całości.

Co dziwne. Błąd (tak, to jest wcześniej wspomniany błąd) tkwi w repozytorium od początku, to znaczy od 26 marca, kiedy to \[chyba\] pierwsza wersja ZF pojawiła się na SVN. Nie muszę chyba przypominać, że w M4 takich wpadek nie ma? ;)
Bardzo wstrętny bug (który zgłosiłem), tym bardziej, że nie trafiłem jeszcze na oprogramowanie, które by leżało u mnie bezpośrednio w document roocie. Zwykle jest tak, że jak coś ściągam to wrzucam do podkatalogu i w nim prowadzę testy. By te testy przeprowadzić z ZF musiałem nieco się namęczyć.

Mam więcej uwag co do samego Zenda, ale o tym w kolejnej notce.. Jeśli ktoś szuka porządnej implementacji MVC, zapraszam do zapoznania się z notką [Mojavi 4, z bliska](/2006/04/25/mojavi-4-z-bliska/).

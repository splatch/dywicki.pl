---
author: splatch
category:
  - framework
  - mvc
  - php
  - wiadomości
date: "2006-02-23T00:40:14+00:00"
guid: http://blog2.dywicki.pl/2006/02/23/why-not-zend-framework/
title: Why not Zend Framework?
url: /2006/02/23/why-not-zend-framework/

---
Patrząc na to co się dzieje wokół ZFa odnoszę bardzo negatywne wrażenie. Dlaczego? Dlatego, że jest to powielanie tego, co już powstało dla PHP. Nie widzę w tym, co proponuje Zend nowości, świerzości. Wiem, że trudno o to przy implementacji MVC, ale ZF nic nie wnosi do tematu, do tego bardzo skromne informacje jakie są na temat ZF nie napawają optymizmem.

Powody dla których Zend tworzy kolejnego frameworka:
 - zachowanie konkurencyjności względem innych języków i platform .NET, J2EE, Ruby
- brak prostego frameworka dla PHP
- swobodne użycie dla zastosowań e-commerce
- pełne wykorzystanie możliwości PHP5
- wsparcie dla nowoczesnych technologii - Web Services, RSS, wsparcie dla AJAXa

Moje uwagi
 - ZF będzie miał bardzo duży problem z dotrzymaniem kroku gigantom - J2EE, .NET
- to co proponuje w dużej mierze jest już w Cake PHP
- CakePHP podobnie jak i Symfony ma bardzo dobrą dokumentację
- praktycznie większość projektów jest opartych na LGPL
- jak by było wiele z tych możliwości PHP5 do wykorzystania..
- Web Services jak i RSS można załatwić przez istniejące już komponenty - bardzo dobry, acz przestarzały, NuSOAP czy też klasy wbudowane w PHP5. RSS nie jest niczym innym jak prostym XMLem który można obsłużyć przy pomocy kolejnej klasy z phpclassess.org. Jeśli Zend chce stworzyć spójną bibliotekę komponentów to nie powinien wynajdować koła na nowo tylko zacząć bliżej współpracować z eZ Systems, które od dłuższego czasu rozwija eZ Components.

Spójrzmy na schemat:
![zend framework](http://shiflett.org/images/zend-framework.png)

Co na nim widać?
 - Front Controller z url-mappingiem. Czyżby odpowiednil <servlet-mapping>? A może od razu action-mapping jak podobnie jak struts-ie?. W chwili obecnej ma to choćby Cake (z przerośniętym request dispatcherem), a sama implementacja tego nie jest na tyle trudna by wymagała tworzenia kolejnego rozwiązania. Gdyby Zend zaproponowałby tutaj możliwość obsługi przez Web Services byłoby to bez wątpienia nowością. Tymczasem Web Services jest przesunięta do komponentów.
- ActionController, czyżby to co proponuje Spring a powoli pojawia się w Mojavi 4? Różne typy akcji w zależności od tego z jakiego kontrolera dziedziczą są bardzo dobrym pomysłem, niestety informacji brak. Jeśli ActionController ma być tylko formalizmem, który nic za sobą nie niesie i wszystkie akcje będą takie same to w tym punkcie ZF przegrywa całą walkę. Proste uruchamianie akcji tego samego typu to nie jest to czego się oczekuje po profesjlonalnym rozwiązaniu.
- Component Library, no kurde eZ components? Tak jak pisałem wcześniej, komponenty które proponuje eZ są na tyle dobrze przemyślane że można rozszeżać bibliotekę o nowe, użyteczne komponenty.
  Przegląd komponentów
   - ZTemplate - jeśli nie będzie miało porównywalnych możliwości jak Smarty to z góry jest skazane na porażkę. IMO dobrym rozwiązaniem byłoby tutaj zastosowanie czegoś na podobę JSP, ASP.NET czy szablonów z Prado bądź samego WACTa - czyli komponenty. Kolejne próby stworzenia systemu szablonów który poza odmienną składnią nie wnosi nic do tematu to zły pomysł.
  - ZForm - nie mam pojęcia co to ma robić, chociaż nie trudno wywnioskować, że chodzi o generowanie kodu HTML z metod. Moim zdaniem to pomyłka - skoro całość zamyka się i tak w szablonie to powinno to być obsłużone poprzez komponenty szablonu a nie oddzielną klasę - WACT ma to bardzo dobrze rozwiązane - dostarcza komponent Form, który w bardzo prosty sposób wpina się w szablon. To, na co Zend powinien zwrócić szczególną uwagę to również walidacja danych - bardzo fajnie jest to zrobione w Mojavi, niestety nie ma wsparcia dla wielu języków, ale skoro Zend sili się na coś dobrego mógłby się przyjrzeć walidacji w Strutsie, gdzie jest możliwość definiowania walidacji warunkowej (np. jeśli pole płeć przyjmuje wartość Kobieta wiek jest nieobowiązkowy). Całość można wyświetlić w szablonie poprzez JSP <html:errors /> i <html:error id="error-id" />. Słowem ZForm to pomyłka.
  - ZAjax - powinien to być komponent szablonu a nie zupełnie oddzielny. Znowu zapewne osadzanie w HTMLu kodu PHP. To nie tędy droga. Jeśli całość ma być rozszeżalna, a szablony na pewno być takie powinny to ZAjax jest złym krokiem. Obsługę AJAXa z poziomu PHP oferuje już Symfony.
  - ZPdf - no może w końcu coś lepszego niż FPDF? :) Ale wciąż dalego do iText, który w tej dziedzinie powinien być wzorem dla twórców ZPdf, wyższy poziom abstrakcji umożliwiający tworzenie nie tylko PDFów ale i dokumentów xsl.
  - ZGetText - powinien być przesunięty do pakiety do obsługi języków. W tej dziedzinie bardzo fajnie zapowiada się Mojavi 4,, gdzie get text będzie tylko jednym z wielu źródeł danych. Słyszałem również, że obsługę języków ma ciekawie rozwiązane Symfony, a i w eZ Components jest pakiet Translation. Zatem sama obsługa get text już na chwilę obecną nie gwarantuje sukcesu.
  - ZActiveRecord - jestem ciekaw jak Zend planuje załatwić relacje 1:N a jeszcze ciekawsze jak M:N. O ile mi wiadomo implementacja takich związków przy użyci ActiveRecordu do najprostszych nie należy. Sam ActiveRecord jest w Cake PHP. Nie jestem pewien czy jest on na tyle rozbudowany co w Rails, ale jeśli tak to jest to kolejne powielenie kodu, które nic nie wnosi. Jeśli Zend poważnie myśli o ORM to powinien przyjrzeć się temu jak Hibernate obsługuje relacje wiele do wielu.
  - ZDBAdapter - rany, kolejny adapter, raz że już jest AdoDB, Pear DB, Pear MDB, Creole, eZ DB, pakiet Database w eZ Components i tak dalej. Pomyłka!
  - ZInputFilter - eZ Components. I znowu, gdzie jest automatyczna walidacja danych..?
  - ZSearch - coś nowego, z przykładów widzę, że całość zapowiada się dość interesująco, ciekawe jak będzie się prezentować od strony wydajności (ponieważ całość jest wzorowana na podobnych rozwiązaniach z Javy).
  - ZAuth - pojęcia brak co by miało to robić.
  - ZAcl - znowu kłania się Cake PHP, w którym jest już obsługa. Jeśli jesteśmy przy uprawnieniach to myślę, że jeśli już ma być to ACL z prawdziwego zdarzenia nie powinien ustępować PHP GACL-owi.
  - ZSession - wtf? Czyżby kolejny komponent który nic poza możliwością zapisu sesji do bazy danych? Jeśli tak to jest to jakaś pomyłka. Myślę, że tutaj Zend powinien zastanowić się nad ideą scope-ów jak w Javie - czyli request, session, page i application.
  - ZLog - jest Log4PHP, fakt że był pisany pod PHP4, ale jest to port do Log4J, które jest jednym z podstawowych pakietów, z których korzystają programujący w Javie. Są również pakiety w PEAR a i loggery w Agavi nie pozostawiają wiele do życzenia.
  - ZConfiguration - eZ Components
  - ZMail - eZ Components
  - ZUrl - co to jest? :)

Reasumując - to co w tej chwili proponuje Zend w swoim rozwiązaniu nie rzuca na kolana. Powiem więcej, mi się to nie podoba.
Zend wydając ZF nie połączy środowiska PHP a je podzieli - podzieli na tych, którzy przekonają się do ZF i na tych, którzy dalej będą wierzyć w inne implementacje MVC. Nawet w tej chwili patrząc na Mojavi 4 mogę bez wątpienia powiedzieć, że będzie więcej oferowało. Ponadto Zend nic nie wspomina o implementacji wzorca Incrementing Filter z J2EE, który nie raz z pożytkiem wykorzystywałem.
Gdy pojawiały się informacje o ZF środowiski huczało o tym, że będzie to próba nawiązania rywalizacji z innymi platformami choćby tylko na warstwie web. W tej chwili mogę powiedzieć, że Zend ze swoim frameworkiem nie jest w stanie tego zrobić.
Owszem, zaoferowanie IDE - owszem jest atrakcyjnym rozwiązaniem, ale nie jest to argument szczególnie przemawiający do mnie. Z początku myślałem, że to co będzie oferował ZF będzie wymagało na prawdę złożonego IDE. Myślałem również, że w celu przyśpieszenia całości ZF będzie zaszyty w źródłach PHP, tymczasem w zupełności starczy phing z zestawem kilku tasków, tak jak jest to zrobione w Agavi czy też Symfony i jak będzie w Mojavi 4 (jeśli te zostanie ostatecznie wdane).
Kolejnym argumentem, który przemawia przeciw ZF jest brak wymuszenia standaryzacji, czyli określony sposób obsługi widoku jak to jest w J2EE (JSP, JSF, Velo City, Free Maker, JSF, czy komponenty w Tapestry). Jeśli PHP ma się rozwijać określonym, dobrym torem to powinny być narzucone pewne normy - jak w przypadku J2EE. Mimo, że jest już tam zaimplementowane MVC samo w sobie to w dalszym ciągu powstają nowe, dobre frameworki.
Niestety ZF będzie rozwiązaniem na tyle niekompletnym i nieudolnym, że choćby próba narzucenia ograniczeń skończyłaby się porażką. Chociaż tutaj chyba nie mam prawa zbytnio winić twórców ZF za to, bo po prostu doszło do takiej a nie innej sytuacji - to znaczy każdy ma swój ulubiony system szablonów. Wiele osób używa Smarty (hm.. Velo City w javie?), ale są również tacy, którzy wolą PHP osadzane w HTMLu (skryplety w JSP?). Po drodze jest jeszcze PHP TAL i kilka innych rozwiązań..

Zainteresowanych zapraszam do dyskusji - co myślicie o Tym, co zaproponował Zend? Czy Wy jesteście przekonani do ZF czy tylko ja jestem nastawiony anty?
Więcej materiałów na temat ZF:
[http://shiflett.org/archive/171 - przykłady z webcasta PHP Architecta](http://shiflett.org/archive/171) [PHP Architect Webcast: The Zend Framework](http://www.phparch.com/webcasts/recordings/dec0205_zend.php) [Oficjalna strona ZF (PHP Collaboration Project)](http://www.zend.com/collaboration)

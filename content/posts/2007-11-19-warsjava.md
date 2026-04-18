---
author: splatch
category:
  - java
  - jaxb
  - wiadomości
date: "2007-11-19T23:16:47+00:00"
guid: http://blog.dywicki.pl/?p=199
summary: W sobotę 17 listopada odbyła się [WarsJava](http://warsjava.pl). Seria warsztatów realizowana przez [Warszawski JUG](http://groups.google.com/group/warszawa-jug), a dokładniej przez dwie osoby (kolejność losowa) Jacka Laskowskiego i Łukasza Świerczyńskiego.
title: WarsJava
url: /2007/11/20/warsjava/

---
W sobotę 17 listopada odbyła się [WarsJava](http://warsjava.pl). Seria warsztatów realizowana przez [Warszawski JUG](http://groups.google.com/group/warszawa-jug), a dokładniej przez dwie osoby (kolejność losowa) Jacka Laskowskiego i Łukasza Świerczyńskiego.

Program prezentacji zapowiadał się i był ciekawy. Przede wszystkim rzecz z którą wiązałem największe zainteresowanie to tandem Spring+OSGi, głównie z racji na podpinanie Springa pod Eclipse RCP. Drugi temat, który był mi bliski to Spring Web MVC + Freemaker, z racji na to, że z jednym i drugim będę miał prawdopodobnie styczność w pracy.
 Trzeci tematem, który budził z mojej strony stosunkowo niskie zainteresowanie było JRuby, jednak sama treść prezentacji i sposób jej prowadzenia spowodowały że wciąż jestem pod wrażeniem tego mariażu Javy i Ruby. Wiktorowi podczas prezentacji wyskoczył tylko jeden chochlik, którego dosyć sprawnie wyeliminował. :)

Na końcu chciałem wspomnieć o tym że i ja miałem swój udział na WarsJavie prowadząc warsztat na temat JAXB. Prawdę powiedziawszy czułem się nieco przerażony - wszyscy podjęli się poważnych tematów, Spring, OSGi, JRuby, JMS podczas gdy ja miałem powiedzieć o bibliotece do robienia obiektów z XMLa i XMLa z obiektów. Szczerze powiedziawszy miałem również spore obawy czy treść, którą przygotowałem będzie w stanie zapełnić półtorej godziny, jakie dostałem na prezentację tematu. Postanowiłem więc dokładnie napisać co jest do zrobienia, dopieścić przykłady, wynikiem czego kładłem się spać o 3 w nocy. Węsząc problemy zawczasu spakowałem laptopa, jak się okazało słusznie - obudziłem się na tyle prędko, że zdążyłem wciągnąć spodnie, wybiec na tramwaj.. Gdy dotarłem na miejsce była już 9:10, dosłownie pięć minut przed rozpoczęciem. Byłaby niezła wpadka gdyby zwiał mi tramwaj i musiałbym czekać 10 minut na kolejny. Szczęście - udało się dotrzeć. Na początku drobne problemy z rzutnikiem, następnie z konfiguracją rozdzielczości.. i tym sposobem uleciało dobre 15 minut.
Parę slajdów, które miałem przygotowane minęło stanowczo zbyt szybko i musiałem siadać do klawiatury. Dziwna rzecz, kiedy człowiek siada, używa tylko dłoni, a mimo wszystko język ma zaplątany. :) Zaiste, problematyczne było pisanie i mówienie. Właściwie to więcej pisałem jak mówiłem. Czas zleciał bardzo szybko, zdążyłem w zasadzie omówić 2 aspekty, na które się gotowałem czyli użycie adnotacji i generowanie kodu ze schematu XSD jak i jeden na który się nie przygotowywałem - radzenie sobie z wyjątkami ;). Nie zdążyłem nic powiedzieć na temat wpływania na zachowanie kompilatora schemy (xjc) poprzez tworzenie plików xjb oraz pisania własnych pluginów. Te punkty staną się najprawdopodobniej tematem przyszłych not.
Szczerze byłem również bardzo mile zaskoczony tym, że ktoś poprosił o kod po prezentacji. :)

Na grupie pojawił się [wątek z opiniami](http://groups.google.com/group/warszawa-jug/t/86c5848c35abd7b) po warsztatach. Niestety nie byłem na [Javarsovii](http://javarsovia.pl) i nie mam punktu odniesienia "co było lepsze". Jakkolwiek zdaje mi się, że tradycyjne prezentacje, gdzie jest kilkanaście slajdów i odrobina kodu zdaje się lepsze. Daje dosyć ogólny pogląd na więcej aspektów niż powstający kod. Z drugiej jednak strony forma warsztatu jest bardziej efektowna, co można było bez wątpienia zaobserwować w przypadku JRuby. Z resztą sama dyskusja tuż po prezentacji Wiktora mogła pokazać jak wiele emocji budzi owe novum. :)

Dla zainteresowanych - zamieściłem [źródła wszystkich projektów](http://media.dywicki.pl/blog/warsjava/jaxb-code.zip) jakie realizowałem przed i w trakcie prezentacji oraz [filmik](http://media.dywicki.pl/blog/warsjava/jaxb-avi.zip) "z kodowania" (nagrany w [Cam Studio](http://camstudio.org/)). Ostrzegam, że avik jest słabo skompresowany i **po wypakowaniu zajmuje 670MB**!

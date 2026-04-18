---
author: splatch
category:
  - inne
  - java
date: "2008-09-28T19:59:10+00:00"
guid: http://blog.dywicki.pl/?p=227
summary: |-
  Pamiętam, jak jakiś już czas temu, kiedy pracowałem w [PZU](http://pzu.pl) dyskutowałem z kolegą na temat [Springa](http://www.springframework.org/). Obaj podziwialiśmy wówczas jego jakość. Chyba wszyscy ludzie którzy mieli styczność z tym narzędziem przyznają, że jest to na prawdę porządnie napisany kawałek kodu. Połączenie bardzo dobrej dokumentacji oraz duża społeczność sprzyjały cały czas Springowi w odnoszeniu kolejnych sukcesów.

  Do dnia 17 września, kiedy to [SpringSource](http://springsource.com) ogłosiło zmianę zasad - i poniekąd [konieczność wykupywania subskrypcji](http://www.springsource.com/node/558). Wiadomość ta zaiste zelektryzowała społeczność.
title: Spring "Commercial" Source zastępuje Open Source
url: /2008/09/28/spring-commercial-source-replaces-open-source/

---
Pamiętam, jak jakiś już czas temu, kiedy pracowałem w [PZU](http://pzu.pl) dyskutowałem z kolegą na temat [Springa](http://www.springframework.org/). Obaj podziwialiśmy wówczas jego jakość. Chyba wszyscy ludzie którzy mieli styczność z tym narzędziem przyznają, że jest to na prawdę porządnie napisany kawałek kodu. Połączenie bardzo dobrej dokumentacji oraz duża społeczność sprzyjały cały czas Springowi w odnoszeniu kolejnych sukcesów.

Do dnia 17 września, kiedy to [SpringSource](http://springsource.com) ogłosiło zmianę zasad - i poniekąd [konieczność wykupywania subskrypcji](http://www.springsource.com/node/558). Wiadomość ta zaiste zelektryzowała społeczność.

Trudno poniekąd dziwić się zmianom w SpringSource ponieważ w [czerwcu tego roku](http://www.springsource.com/node/393) do grona właścicieli dołączyła kolejna spółka typu [venture capital](http://pl.wikipedia.org/wiki/Venture_capital). Chyba podobnie tak jak w przypadku przejęcia BEA przez Oracle konieczne było odbicie sobie kosztów podnosząc ceny. Póki co Spring Application Platform nie ma wielkiego przebicia na rynku serwerów, ponieważ jest (chyba?) rozwiązaniem zbyt rewolucyjnym, na wsparcie wielu klientów się nie decydowało, szkolenia organizuje nie tylko SpringSource ale też cała masa innych firm, więc de-facto źródeł dochodów producent Springa nie miał wielu. Po prostu ilość użytkowników tego frameworka nie przekładała się w widoczny sposób na zasobność kiesy. Inwestorzy wykorzystując popularność narzędzia chcą skierować wielki strumień pieniędzy prosto do swojej kieszeni. Oczywiście pieniądze te nie będą przeznaczone tylko na honoraria ale również na dalszą ekspansję na rynku. Już teraz wiadomo, że Spring 3.0 będzie zgodny ze specyfikacją EJB 3.1, co zwiastuje koniec wojny [EJB vs Spring](http://www.ryandelaplante.com/rdelaplante/entry/end_of_spring_vs_ejb). Pod w/w linkiem jest też kilka komentarzy na temat zmian:

> To smutne, ale kończę wierzyć marzyć, pora spojrzeć na wszystko trzeźwo, standardy nie są złe, są oparte na idei Springa, dzięki Bogu.

> Nie będę płacił myta za korzystanie z kontenera DI. Jeśli będię potrzebował portfolio SpringSource pomyślę czy zapłacić, ale żebym musiał płacić za sam kontener DI? Nie ma mowy, wolę poczekać na "STANDARD" EJB 3.1 albo przesiąść się na Guice czy nawet kontener DI z Tapestry 5

> Jak ktoś już powiedział kontener DI Springa nie jest jakimś nadzwyczajnym wynalazkiem, może powinniśmy zacząć używać Guice bądź nadchodzącego EJB 3.1?

> Pi\*\* SpringSource!! TO wszystko co powiem. Jesteśmy społecznością i to my pomogliśmy wyrosnąć Spinrgowi a traz oni wbijają nam nóż w plecy. Witam EJB 3.1 jako standard i myślę, że powinienem zacząć poważnie myśleć o Seamie bądź innej alternatywie, może Guice? SpringSource tylko się pogrąża. Panie Rod, miałem Cię za naszego zbawiciela ale okazałeś się takim samym du\*\*\* jak wszystko w Open Source

> Niestety, sterowany marketingiem szum ("the de facto standard in enterprise Java") wokół Springa w końcu dobiegł końca. Świetnie!

Więcej opinii znajdziecie jeszcze na [The Server Side: New Spring maintenance policy](http://www.theserverside.com/news/thread.tss?thread_id=50727).

## Moim skromnym zdaniem

Nie pisał bym o tym wszystkim gdybym nie miał na to własnego zdania. Oczywiście szkoda, że losy Springa teraz idą w kierunku niezbyt sprzyjającym dla większości z nas. Z drugiej jednak strony gwarancja łatania dziur w celu zapewnienia stabilności działania aplikacji produkcyjnych jest kusząca i odciąża developerów. Jakkolwiek nie ma tu już równowagi, ponieważ wg. nowych założeń albo płacisz i masz wszystkie poprawki albo nie płacisz i masz tylko główne wydania (major releases). Czy znajdzie się ktoś kto zacznie rozwijać alternatywę dla Springa?

Nie chodzi mi tu o sam kontener DI bo zdaję sobie doskonale sprawę z tego, że Spring już od bardzo dawna przestał być tylko nośnikiem DI. Masa modułów - wspierających pracę z bazami danych, zarządzanie transakcjami, moduły do JMS, web services, EJB czy nawet integracji. To wszystko miało swoich nabywców. Osobiście zmiana ta średnio mi odpowiada, ponieważ dosłownie zmusza do wykupienia wsparcia (wyobraża sobie ktoś korzystanie ze Springa tylko w wersjach 2.5, 2.0, 1.2, 1.1, 1.0?). W tej chwili nie ma żadnego zastępnika dla Springa i jego inwestorzy doskonale sobie z tego zdają sprawę. Po wejściu EJB 3.1 wszystko się rozmyje, a póki co jest bardzo dobra okazja do łowienia klientów.

Model licencjonowania w przypadku [Service Mixa](http://servicemix.apache.org) jest jasny - jest za darmo. Ma dokumentację jaką ma, ma dziury, są z nim oczywiście problemy, ale [Iona](http://open.iona.com) na jego bazie dostarcza produkt i wsparcie do niego. Nikt nie ma obowiązku korzystania z usług Iony, w ogóle nie musi o jej istnieniu wiedzieć. Jeśli ktoś będzie potrzebował wsparcia to je i tak przecież kupi!

Różnicą i to znaczną są wyraźne ambicje SpringSource by walczyć o miano pretendenta na rynku serwerów z IBM i BEA. Problem w tym, że w tym momencie jest on pozbawiony silnego argumentu, jakim był kod Open Source, w obecnej formie jest to po prostu wypaczenie idei otwierania kodu. Społeczność zdaje się, że jest zniesmaczona, ale cóż zrobić.

**Za jakość się płaci i nauczka jest taka że w przypadku Springa przychodzi nam płacić po znacznie dłuższym czasie.**

---
author: splatch
category:
  - java
  - tlumaczenia
  - wiadomości
date: "2008-12-22T10:35:47+00:00"
guid: http://blog.dywicki.pl/?p=243
summary: Dnia wczorajszego na [InfoQ](http://infoq.com) o tym, że został udostępniony [szkic specyfikacji](http://www.infoq.com/news/2008/12/servlet3_debate) Servlet API 3.0. Największe nowości to... adnotacje, które będą mogły być użyte w miejsce interfejsów i dziedziczenia.
title: Java Servlet API 3.0
url: /2008/12/22/java-servlet-api-3/

---
Dnia wczorajszego na [InfoQ](http://infoq.com) o tym, że został udostępniony [szkic specyfikacji](http://www.infoq.com/news/2008/12/servlet3_debate) Servlet API 3.0. Największe nowości to... adnotacje, które będą mogły być użyte w miejsce interfejsów i dziedziczenia.

> - Łatwiejsze tworzenie, ang. _Ease of Development_ (Eod): W wcześniejszym szkicu zostały zaprezentowane adnotacje jako sposób na stworzenie Servletu - POJO. Jakkolwiek po dyskusji i odpowiedzi społeczności adnotacje @GET i @POST na poziomie metod zostały wycofane. Zostają jednak adnotacje dla typów - deklaracja sevletu @WebServlet oraz deklaracja filtru @ServletFilter, @WebServletContextListener będzie alternatywnym sposobem na zdefiniowanie context listenera. Dodatkowo adnotacje takie jak @Resouorce będą wspierane w aplikacjach webowych będą zachowywać się tak jak w starszej wersji specyfikacji (2.5).
> - Rozszerzalność: szkielety webowe zbudowane na bazie servletów są bardzo popularne. Jest ich bardzo wiele, do wyboru w zależności od problemów, które mają rozwiązywać. Specyfikacja będzie umożliwiała łatwiejszą konfigurację tychże szkieletów.
> - Przetwarzanie asynchroniczne: największym wyzwaniem i zmianą jaką podjęli autorzy specyfikacji. W poprzedniej wersji zostały zdefiniowane operacje zatrzymania (suspend) oraz wznowienia (resume). Po publikacji grupa ekspertów podjęła dyskusję na temat przypadków użycia takiego rozwiązania.

Na łamach [InfoQ](http://www.infoq.com/news/2008/12/servlet3_debate) są fragmenty dyskusji na temat fragmentów specyfikacji. Ludzie z JBossa krytykują Jetty, inni krytykują grupę która pracuje nad specyfikacją, że kiepsko współpracuje ze społecznością.

Mam osobiście nadzieję, że ta burza mózgów wyjdzie nam na dobre i adnotacje w Servlet API 3.0 nie będą jedyną nowością. :-)

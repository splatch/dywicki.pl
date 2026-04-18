---
author: splatch
category:
  - java
  - mvc
date: "2006-03-02T01:08:36+00:00"
guid: http://blog2.dywicki.pl/2006/03/02/troszk-inne-mvc/
title: Troszkę "Inne" MVC
url: /2006/03/02/troszk-inne-mvc/

---
Jakiś czas temu, gdy walczyłem ze swoimi pierwszymi servletami dzięki koledze zetknąłem się ze springiem. Wiedziałem, że ten framework istnieje, ale dopiero z jego pomocą stworzyłem pierwszą akcję. Idea jest bardzo prosta - na podstawie pliku beans.xml kontener IoC dopasowywuje klasę do adresu. Może być też tak, że mapowane są nazwy beanów do adresu - np. <bean id="/foo.html" />>.
To co mnie oczarowało w springu to świetne rozwiązania. Bardzo spodobały mi się zróżnicowane akcje. To już nie jest Cake PHP, gdzie nazwa ApplicationController ma znaczenie bardziej symboliczne niż praktyczne czy Struts ze swoimi biernym akcjami. Przy potencjale jaki oferują kontrolery Springa wiele frameworków wymięka. Poniżej prezentuje diagram klas wykonany w programie Dia obrazujący powiązania kontrolerów we wcześniej zachwalanym frameworku.
![Diagram klas](http://img.dywicki.pl/Diagram1.dia.png)

Całość działa bardzo fajnie - w zależności od klasy z której dziedziczymy mamy różne rodzaje akcji. Np. SimpleFormController to typowy układ wprowadź+pokaż błędy+wykonaj akcję. AbstractWizzardController wykorzystuje się, gdy mamy kilkukrokowe formularze zapytań wykorzystujemy BaseCommandController jeśli tych kroków jest o wiele więcej wykorzystujemy AbstractWizzardController.
Kontroler zawsze zwraca obiekt ModelAndView, który zawiera informację jaki widok ma być wykonany oraz dane, które mają do niego trafić. Dzięki temu jeden widok może być uruchamiany przez wiele akcji przy zmienionych danych. Układ jest bardzo dobry, zawsze brakowało mi czegoś takiego w Mojavi, które pod względem widoków jest trochę w tyle ponieważ wymusza tworzenie wielu plików i utrudnia ponowne wykorzystanie choćby samych szablonów.

Zrobiłem również prosty diagram sekwncji by pokazać jak wygląda obsługa wywołania. Niestety Dia nie dostarcza żadnych komponentów z których się przy takich diagramach korzysta i stąd taka a nie inna jakość niżej załączonego obrazka:
![Diagram klas](http://img.dywicki.pl/Diagram2.dia.png)

Co daje nam zastosowanie elementu pośredniczącego jakim jest ModelAndView? Daje możliwość zastosowania dodatkowego ogniwa, którego na tym diagramie nie ma ponieważ jest to robione w Dispatcher Servlet, to znaczy View Resolverów. View Resolver to element który w rzeczywistości uruchamia widok. Na podstawie przekazanej nazwy widoku dodaje prefix, sufix i sprawdza czy plik istnieje. Jeśli istnieje to uruchamia widok z danymi, które zwróciła akcja, jeśli nie zwraca false a DispatcherServlet uruchamia kolejnego VewReoslvera w celu odnalezienia widoku. Kolejność można określić w konfiguracji. Można mieć kilka resolverów, można mieć jeden. Jeśli nie zostanie odnaleziony widok Dispatcher Servlet wywali odpowiedni wyjątek.
To rozwiązanie daje możliwość tworzenia wielu typów widoku, obsługiwanego poprzez różne mechanizmy dla jednej akcji. Bez zmian w akcji można zmienić format outputu oraz to czy obsłuży go nam JSP bądź Freemaker. Czyż to nie cudowne? :)
Możliwe, że całość opisałem dość niejasno, dlatego proszę - jeśli trafi się tutaj osoba znająca springa - o sprostowania.

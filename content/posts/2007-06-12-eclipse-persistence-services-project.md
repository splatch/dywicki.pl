---
author: splatch
category:
  - eclipse
  - framework
  - wiadomości
date: "2007-06-12T23:11:19+00:00"
guid: http://blog.dywicki.pl/?p=144
summary: Dzisiaj (w zasadzie wczoraj) w otchłani skrzynki odbiorczej [RSSOwl](http://rssowl.org/) znalazłem [link do propozycji](http://www.eclipse.org/proposals/eclipselink/) wspomnianego projektu.
title: Eclipse Persistence Services Project
url: /2007/06/13/eclipse-persistence-services-project/

---
Dzisiaj (w zasadzie wczoraj) w otchłani skrzynki odbiorczej [RSSOwl](http://rssowl.org/) znalazłem [link do propozycji](http://www.eclipse.org/proposals/eclipselink/) wspomnianego projektu.

Czym ma on być? Ma być ujednoliconym szkieletem umożliwiającym programistom dostęp do baz danych, dokumentów XML jak i zewnętrznych systemów pokroju [EAI](http://en.wikipedia.org/wiki/Enterprise_application_integration) przy użyciu istniejących technologii tj. [Java Persistence API](http://java.sun.com/javaee/technologies/persistence.jsp) (JPA), [Java Architecture for XML Binding](http://java.sun.com/xml/downloads/jaxb.html) (JAXB), [Java Connector Architecture](http://java.sun.com/j2ee/connector/download.html) (JCA), and [Service Data Objects](http://www.ibm.com/developerworks/java/library/j-sdo/) (SDO). Cel ma być uzyskany we współpracy ze specjalistami od [OSGi](http://www.osgi.org/) przy pomocy implementacji przykładowych implementacji, które pokażą jak używać wcześniej wymienionych interfejsów. Dzięki oparciu całości na platformie OSGi pomysłodawcy chcą zyskać niebywałą do tej pory w tego typu projektach przenośność i modularność co w połączeniu ma zaowocować mariażami (a może mezaliansami) różnego rodzaju.
Na stronie z propozycją jest prosty schemat, który wstępnie obrazuje architekturę szkieletu:

[![Eclipse Persistence Services Project](/wp-content/uploads/2007/06/eclipselink.gif)](/wp-content/uploads/2007/06/eclipselink.gif "Eclipse Persistence Services Project")

Warto zwrócić uwagę na to, że całość projektu nie będzie uzależniona od Eclipse jako takiego a jest tylko rozwijana w ramach [fundacji eclipse](http://www.eclipse.org/org/foundation/). Całość będzie można używać zarówno z poziomu [Javy EE](http://java.sun.com/javaee/) jak i Javy SE (jak domniemywam również Swing).

Głównym pomysłodawcą projektu jest [Oracle](http://www.oracle.com/) z którego ramienia będzie póki co pracować najwięcej developerów, głównie tych, którzy wcześniej zajmowali się [TopLinkiem](http://www.oracle.com/technology/products/ias/toplink/index.html). Jakkolwiek w deklaracji pod koniec propozycji pada zdanie czy też zaproszenie - drzwi są otwarte dla chętnych. :)

Osobiście jestem bardzo ciekaw efektów jakie przyniesie ten projekt, ponieważ znacznie by on ułatwił prace nad aplikacjami stricle biznesowymi opartymi na [Eclipse RCP](http://wiki.eclipse.org/index.php/Rich_Client_Platform) z racji na to, że wystarczy podpiąć się do dostarczonych usług OSGi by móc korzystać z bazy danych czy też wyciągać dane z jakiegoś podsystemu. Fajnie by było uprościć walki, powiedzmy z Hibernate i jego używaniem pod RCP.
Dodam, że Eclipse Persistence Services to kolejny "dość egzotyczny" projekt realizowany w ramach fundacji nie związany ściśle z platformą Eclipse - wystarczy wspomnieć [Eclipse Communication Framework](http://www.eclipse.org/ecf/) z inkubatora, który w wersji 1.0 wchodzi już w skład najbliższego zbiorczego wydania - [Europy](http://www.eclipse.org/europa/projects.php)

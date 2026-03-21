---
author: splatch
category:
  - uncategorized
date: "2008-06-22T20:23:35+00:00"
guid: http://blog.dywicki.pl/?p=215
summary: Jakiś czas temu, jeszcze podczas pracy w poprzedniej firmie przypadło mi zadanie podpięcia się pod magistralę usług opartą o [Apache Service Mix](http://servicemix.apache.org) (SMX). Był to wówczas dla mnie temat zupełnie nowy, ba nawet nie wiedziałem z czym to się je. :) Koniec końców jednak podpięcie pod ESB (Enterprises Service Bus) nie było w ogóle trudne. Po jakimś czasie i drobnych przetasowaniach na płaszczyźnie zawodowej zająłem się SMX-em nie jako klient magistrali a osoba implementująca usługi na szynie a ten wpis jest drobną przeróbką prezentacji, którą przygotowałem w pracy.
title: Apache ServiceMix, Open Source ESB
url: /2008/06/apache-servicemix-open-source-es/

---
Jakiś czas temu, jeszcze podczas pracy w poprzedniej firmie przypadło mi zadanie podpięcia się pod magistralę usług opartą o [Apache Service Mix](http://servicemix.apache.org) (SMX). Był to wówczas dla mnie temat zupełnie nowy, ba nawet nie wiedziałem z czym to się je. :) Koniec końców jednak podpięcie pod ESB (Enterprises Service Bus) nie było w ogóle trudne. Po jakimś czasie i drobnych przetasowaniach na płaszczyźnie zawodowej zająłem się SMX-em nie jako klient magistrali a osoba implementująca usługi na szynie a ten wpis jest drobną przeróbką prezentacji, którą przygotowałem w pracy.

### Czym jest ESB

Jednoznaczne określenie terminu ESB nie jest łatwe, ponieważ wokół tego tematu rozpętana została burza marketingowa. Jedni uważają je za oś SOA (Service Oriented Architecture) inni jako zło konieczne w dużych instytucjach.

Dlatego pomijając teorię przejdźmy do najistotniejszych cech, jakie oferuje ESB, niezależnie od producenta oraz osoby definiującej pojęcie. Sam nie chciałbym wdawać się w dyskusję na temat postrzegania i ESB i SOA.

![](/wp-content/uploads/2008/06/esb.jpg)_Źródło - [CodePlex](http://www.codeplex.com/esb)_

Na załączonym wyżej obrazku widać typową strukturę logiczną w oparciu o ESB. Z lewej strony mamy komercyjne rozwiązanie – [MQ Series](http://www-306.ibm.com/software/integration/wmq/) firmy [IBM](http://www.ibm.com/us/), dalej idąc dołem, widzimy bazę danych, serwer pocztowy a na końcu mainframe. U góry natomiast pojawiają się klienci.

### Ciąg dalszy

Na bazie tego obrazku można powiedzieć co nieco o tym, czym owa niebieska rurka symbolizująca ESB jest:

- Jest to z pewnością centralny rejestr usług firmy, dzięki któremu nie jest konieczne wiązanie aplikacji między sobą. Wiąże się je tylko i wyłącznie z jednym elementem – z magistralą.
- Kolejny ważny punkt, to most pomiędzy protokołami. W dobie, gdy wszyscy żyją już Web Services nie można zapomnieć o innych sposobach komunikacji – poczynając od poczciwej [Corby](http://pl.wikipedia.org/wiki/CORBA) po kolejki JMS czy też odczyt zasobów z FTP.
- Transformacja komunikatów to opcjonalna czynność, której nie widać na wyżej wymienionym obrazku. Jest ona wykonywana pod maską, wewnątrz magistrali, w zależności od potrzeb. W chwili gdy mamy komunikaty z systemu A do systemu B możemy wszystko sprowadzić do jednego uniwersalnego API danych.
- Inteligentny router informacji. Większość magistral ma coś wspólnego z pojęciem EIP, czyli [Enterprise Integration Patterns](http://www.enterpriseintegrationpatterns.com/). Jednym z tych wzorców jest [Content Based Router](http://www.enterpriseintegrationpatterns.com/ContentBasedRouter.html), to znaczy w zależności od kształtu, zawartości komunikatu, nagłówka, fragmentu, czegokolwiek możemy odbijać komunikat w różnych kierunkach. Dalej z wzorców można wymienić [Message Filter](http://www.enterpriseintegrationpatterns.com/Filter.html), [Recipient List](http://www.enterpriseintegrationpatterns.com/RecipientList.html), [Routing Slip](http://www.enterpriseintegrationpatterns.com/RoutingTable.html), [Wire Tap](http://www.enterpriseintegrationpatterns.com/WireTap.html), [Splitter](http://www.enterpriseintegrationpatterns.com/Sequencer.html), [Resequencer](http://www.enterpriseintegrationpatterns.com/Resequencer.html) itd.
- Integrator procesów biznesowych (bardziej marketingowo) – po pierwsze odwzorowanie usług magistrali do czynności biznesowych w organizacji a po drugie wsparcie dla procesorów reguł biznesowych ( [WS-BPEL](http://www.oasis-open.org/committees/tc_home.php?wg_abbrev=wsbpel)).

### Service Mix jako ESB

Mając zestaw wyżej wymienionych cech możemy przejść do omówienia projektu Apache Service Mix.
Może na początku kilka słów o tym, czym jest JBI, ponieważ pojawia się sam skrót, ale nie ma jego omówienia.
Otóż, [JBI](http://jcp.org/en/jsr/detail?id=208) w rozwinięciu oznacza Java Business Integration. Jest to standard przyjęty w ramach [Java Community Process](http://jcp.org/) w celu określenia norm budowania rozwiązań SOA (znów buzzword). Pomijając politykę wielkich korporacji oraz marketing przejdźmy do elementów, które standard ten określa:
![Źródło: Java.net](/wp-content/uploads/2008/06/msgflow3.png)_Źródło [Open ESB Starter Kit](http://download.java.net/general/open-esb/docs/jbi-admin-guide/)_

- Typy komponentów:
  1. Service Engine (SE) – backend do obsługi zapytań.
  1. Binding Components (BC) – frontend, do nasłuchiwania w danym standardzie.
  1. Shared Libraries (SL) – kod współdzielony przez w/w komponenty.
  1. Service Assembly (SA) – zbiór usług rozumianych jako jedność przez magistralę (zwykle para BC+SE).
- Normalized Message Router – jest to serce rozwiązania opartego o JBI, ponieważ w nim są transportowane komunikaty. To on zapewnia przepływ informacji z komponentów bindujących do silników.
- Message Exchange Patterns – w oparciu o definicję dla SOAP JBI przewiduje następujące typy komunikatów:
  1. In-Only – tylko wejście, usługa nie zwraca żadnej odpowiedzi
  1. Rebust In-Only – zwrócony zostanie status po obsłudze zapytania bądź wyjątek.
  1. In-Out – standardowa obsługa wejście-wyjście.
  1. In Optional-Out – wejście z niewiążącą (nieobowiązkową) odpowiedzią.

Dostępnych jest kilka implementacji JBI:

- [Open ESB](https://open-esb.dev.java.net/)
- [Apache Service Mix](http://servicemix.apache.org)
- [FUSE ESB](http://open.iona.com/products/enterprise-servicemix/) (na bazie Service Mix)
- [Bostech Chain Builder ESB](http://www.bostechcorp.com/products_cb_esb.htm)
- [Mule](http://mule.mulesource.org/display/MULE/Home)
- [JBoss ESB](http://www.jboss.org/jbossesb/)
- [Fusion Middleware](http://www.oracle.com/products/middleware/index.html)
- [ActiveMatrix Service Bus](http://www.tibco.com/software/soa/activematrix_service_bus/default.jsp)

### Service Mix od środka

Wewnątrz Service Mix jest spięciem kilku potężnych projektów, rozwijanych od dłuższego czasu, które zdobyły już renomę i popularność. Między innymi można wyróżnić:

- Pierwszy z tych projektów to [Spring Framework](http://springframework.org/), rozwijany od bodajże 2000 roku, z powodzeniem rywalizujący z architekturami opartymi o EJB. Spring jest nie tylko mechanizmem konfiguracyjnym ale również zbiorem bardzo dobrych komponentów umożliwiających szereg operacji (bazy danych, JMS, przetwarzanie wsadowe, Web Services etc).
- Drugi, bardzo ważny projekt to [Active MQ](http://activemq.apache.org/). Największa i najpopularniejsza otwarta implementacja standardu JMS. Jest on używany wewnątrz Service Mix-a jako transporter komunikatów w Normalized Message Router jak i do obsługi końcówek JMS.
  \- Wymieniony nieco niżej pod-projekt Active MQ to [Camel](http://activemq.apache.org/camel/). Jest to szkielet przeznaczony do tworzenia reguł routingu. Wspiera różnorakie transporty (HTTP, JMS, JBI, MS MQ itp.).
- [XBean](http://geronimo.apache.org/xbean/) jest fragmentem projektu Apache Geronimo (serwer aplikacyjny ze stajni Apache) przeznaczonym do tworzenia konfiguracji i zarządzania komponentami. Jest zbudowany w oparciu o Springa.
- [Apache CXF](http://cxf.apache.org/) jest stosunkowo nowym projektem, który jest używany poprzez Service Mix w celu obsługi zapytań SOAP (chociaż możliwe jest użycie innego komponentu).
- [Apache ODE](http://ode.apache.org/) jest silnikiem reguł biznesowych w oparciu o WS-BPEL.
- JBoss Drools jest kolejnym procesorem reguł biznesowych. Może być użyty do routingu. Jest dostępny plugin pozwalający na łatwą pracę z tą technologią.

### Możliwości Service Mix

Wyżej wymienione projekty są używane w Service Mix w celu uzyskania typowych funkcjonalności ESB:

- [JMS](http://servicemix.apache.org/servicemix-jms.html), czyli obsługa kolejek
- [WS-BPEL](http://servicemix.apache.org/servicemix-bpe.html), obsługa reguł biznesowych
- Web Services przy pomocy [CXF](http://servicemix.apache.org/servicemix-cxf-se.html) jak i modułu [JSR-181](http://servicemix.apache.org/servicemix-jsr181.html)
- [Transformacje](http://servicemix.apache.org/servicemix-saxon.html) XSLT oraz XQuery (w oparciu o Saxona)
- [File Drop](http://servicemix.apache.org/servicemix-file.html) to odczyt i zapis do plików dostępnych lokalnie jak i zdalnie ( [FTP](http://servicemix.apache.org/servicemix-ftp.html))
- Obsługa protokołu [XMPP](http://servicemix.apache.org/servicemix-xmpp.html) pozwala na łatwą integrację z komunikatorami zbudowanymi w oparciu o Jabbera.
- Dostęp do poczty przy pomocy modułu [servicemix-mail](http://servicemix.apache.org/servicemix-mail.html)
- Komponenty programowe:
  - Dają [możliwość dopisania](http://servicemix.apache.org/servicemix-bean.html) własnych "endpointów", czyli implementacji docelowych usług bądź pośredników.
  - Dodatkowe funkcjonalności (cache, rss, walidacja)
- [Języki skryptowe](http://servicemix.apache.org/servicemix-scripting.html) (min [Groovy](http://groovy.codehaus.org/))

### ESB – dlaczego Open Source

Wybór Service Mix-a nie był podyktowany przypadkiem. Jest to bowiem najpopularniejsze tego typu rozwiązanie z otwartym kodem źródłowym. Co więcej, nie jest to projekt rozwijany przez osoby bez doświadczenia, pozostawiony bez wsparcia.
Otwarty kod ułatwia przede wszystkim adaptację tego rozwiązania do potrzeb organizacji a nie odwrotnie – organizacji do potrzeb magistrali. W razie potrzeb jesteśmy w stanie dopisać własne komponenty, obsługę mniej standardowych protokołów na bazie dostarczonych interfejsów czy to w Service Mix czy to w Camelu.
Przyjęte standardy gwarantują ciągłość rozwoju oraz ułatwiają integrację ( [JMX](http://java.sun.com/javase/technologies/core/mntr-mgmt/javamanagement/) umożliwia łatwe podpięcie konsoli administracyjnej), podczas gdy [J2EE Connector Architecture](http://en.wikipedia.org/wiki/J2EE_Connector_Architecture) definiuje kontrakty (zarządzanie połączeniami, transakcjami, bezpieczeństwem).
Nie bez znaczenia jest również koszt, jaki organizacja ponosi w przypadku zdecydowania się na otwarte rozwiązanie. Rozpoczęcie prac z Service Mix-em kosztuje 0 PLN. Każdy, bez rejestracji, podawania jakichkolwiek danych może pobrać źródła albo gotowe dystrybucje i uruchomić je na swoim komputerze. W chwili gdy istnieje takie zapotrzebowanie, organizacja posiada kompetencje i skromny budżet to taka konfiguracja początkowo jest optymalna. Z biegiem czasu gdy zaistnieje konieczność wsparcia czy szkoleń to są one oferowane przez firmę IONA, która jest zaangażowana w rozwój Service Mix-a.

### Service Mix a bezpieczeństwo

Większość, jeśli nie wszystkie rozwiązania w Javie, które wiążą się z kryptografią są oparte na JCA – [Java Cryptography Architecture](http://java.sun.com/javase/6/docs/technotes/guides/security/crypto/CryptoSpec.html). Jest to zestaw interfejsów oraz ich implementacji zawierający implementację najpopularniejszych algorytmów kryptograficznych jak i API umożliwiające tworzenie własnych rozszerzeń ( [JCE](http://java.sun.com/javase/technologies/security/)).
Standard autoryzacji i uwierzytelniania w Javie to JAAS ( [Java Authentication and Authorization Service](http://java.sun.com/javase/6/docs/technotes/guides/security/jaas/JAASRefGuide.html)). W oparciu o niego jest budowanych większość rozwiązań związanych z bezpieczeństwem. Nawet największe alternatywy takie jak [Acegi Security](http://www.acegisecurity.org/) (obecnie Spring Security posiadają adaptery integrujące je ze standardem). Przy użyciu dostępnych interfejsów możliwe jest dostarczenie własnej implementacji usługi obsługującej autoryzację bądź uwierzytelnianie użytkowników/systemów.
Bezpieczeństwo usług sieciowych jest zależne od wybranego komponentu Service Mix. Pełne wsparcie dla WS-Security oferują komponenty zbudowane w oparciu o Apache CXF (szyfrowanie, podpisywanie komunikatów). Z innych standardów CXF wspiera także WS-Policy, WS-Addressing.
Szyfrowane połączenia są łatwe do uzyskania przy pomocy komponentu [servicemi-http](http://servicemix.apache.org/servicemix-http.html).
Dostępne zabezpieczenia na poziomie wirtualnej maszyny Javy to certyfikowanie (podpisywanie) kodu modułów oraz konfiguracja Security Managera (umożliwia wyłączenie dostępu do pakietów/klas/metod).

---
author: splatch
category:
  - uncategorized
date: "2010-03-22T12:19:50+00:00"
guid: http://blog.code-house.org/?p=179
title: Wprowadzenie do Apache ServiceMix 4 cz. 1
url: /2010/03/introduction-to-apache-servicemix4-part-1/

---
Dnia 23 lutego w ramach Warszawa JUG miałem przyjemność wraz z [Tomkiem Nurkiewiczem](http://nurkiewicz.blogspot.com/) prezentować narzędzia integracyjne z otwartym kodem źródłowym. Tomek przedstawił Mule ESB, podczas gdy ja zająłem się Apache ServiceMix i Apache Camel. Ze względu na objętość przykładu ten wpis będzie jedynie wprowadzeniem do konsoli.

Niestety podczas prezentacji nie udało mi się uruchomić przykładu na "szynie" - ponieważ uniemożliwiły to zależności do bibliotek których nie miałem zapisanych lokalnie. Drugim mym przeciwnikiem był czas - nie było wielu chętnych by słuchać po 2h tłumaczeń dlaczego się nie udało :-) Na problem z zależnościami stworzyłem rozwiązanie i zgłosiłem je do Karaf-a ( [FELIX 2141](https://issues.apache.org/jira/browse/FELIX-2141)). W przyszłej wersji - 1.6 - wszyscy będą mogli skorzystać z polecenia `features:info -t` które wyświetli całe drzewko zależności potrzebnych do zainstalowania nowych funkcjonalności.

### Przygotowanie środowiska

Do uruchomienia przykładów potrzebować będziemy dwóch paczek - pierwsza to [Apache ServiceMix](http://servicemix.apache.org), druga to [Apache Maven](http://maven.apache.org). Ze swojej strony polecam pobranie [FUSE ESB 4.2](http://fusesource.com/downloads/), produktu który jest oparty o ServiceMix. Zgodnie z moimi zapowiedziami od tej wersji FUSE Source wprowadza pełne wsparcie produkcyjne dla ServiceMix 4. Jeśli nie masz zainstalowanego Mavena i nigdy tego nie robiłeś zajrzyj na wiki [Code-House](http://inside.code-house.org/confluence/): [Wprowadzenie do Maven 2](http://inside.code-house.org/confluence/display/MVN/Wprowadzenie+do+Maven+2)

Po pobraniu odpowiedniej wersji należy ją rozpakować do wybranego folderu. Szynę uruchamiamy skryptem servicemix.bat. Jeśli wszystko przebiegło poprawnie naszym oczom powinien ukazać się obrazek jak poniżej:

```bash
E:/tools/progressfuse-esb4.2.0-b/bin>servicemix.bat
 ____                  _          __  __ _
/ ___|  ___ _ ____   _(_) ___ ___|  /  (_)_  __
___  / _  '__  / / |/ __/ _  |/| |  / /
 ___) |  __/ |    V /| | (_|  __/ |  | | |>  <
|____/ ___|_|    _/ |_|______|_|  |_|_/_/_

  Apache ServiceMix (4.2.0-fuse-01-00)

Hit '' for a list of available commands
and '[cmd] --help' for help on a specific command.
karaf@root>

```

Od tej chwili mamy do dyspozycji konsolę administracyjną. Aby zwiększyć jej użyteczność wykonujemy następujące polecenia:

```bash
karaf@root> osgi:install wrap:mvn:http://download.java.net/maven/2!net.java.dev.jna/jna/3.1.0
Bundle ID: 134

```

Spowoduje to pobranie z repozytorium Mavena (http://download.java.net/maven/2) biblioteki JNA w wersji 3.1.0. Nie musimy oczywiście określać za każdym razem adresu repozytoriów, ale o tym nieco później. Prefix wrap: spowoduje wygenerowanie manifestu OSGi na podstawie zawartości pobranej biblioteki. Jest on konieczny ponieważ JNA nie dostarcza potrzebnych danych do uruchomienia w środowisku OSGi.

```bash
karaf@root> osgi:install mvn:http://jansi.fusesource.org/repo/release!org.fusesource.jansi/jansi/1.2
Bundle ID: 135

```

Druga biblioteka wykorzystuje JNA i umożliwia kolorowanie tekstu w konsoli windowsowej zgodnie z ANSI, czyli tak jak w standardowych terminalach Unix-a. Po zainstalowaniu tych dwóch rzeczy pora zaprząc je do pracy. Poniżej filtrujemy listę zainstalowanych rzeczy po słowie Console.

```bash
karaf@root> list|grep Console
[  10] [Active     ] [Created     ] [       ] [   30] Apache Felix Karaf :: Shell Console (1.4.0.fuse-01-00)
karaf@root> refresh 10

```

Numer 10 oznacza id paczki OSGi, tekst Active to stan paczki a tekst Created informuje o stanie kontekstu blueprint, czwarty bloczek to stan kontekstu Springa, numer 30 to start level paczki a w ostatnim nawiasie mamy wersję. Sporo informacji jak na jedną linijkę, nieprawdaż? Blueprint to standaryzowanie tego czym jest Spring i Spring DM, także można korzystać zamiennie bądź z jednego bądź z drugiego rozwiązania. Ciekawe porównanie funkcjonalności obu rozwiązań - Spring DM oraz Blueprint opublikował kilka dni temu Guillaume Nodet na swoim blogu we wpisie [Spring-DM, Aries Blueprint and custom namespaces](http://gnodet.blogspot.com/2010/03/spring-dm-aries-blueprint-and-custom.html). Apache Camel od wersji 2.3 będzie wspierał Blueprint ( [CAMEL-2022](https://issues.apache.org/activemq/browse/CAMEL-2022)).

```bash

 ____                  _          __  __ _
/ ___|  ___ _ ____   _(_) ___ ___|  /  (_)_  __
___  / _  '__  / / |/ __/ _  |/| |  / /
 ___) |  __/ |    V /| | (_|  __/ |  | | |>  <
|____/ ___|_|    _/ |_|______|_|  |_|_/_/_

  Apache ServiceMix (4.2.0-fuse-01-00)

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
karaf@root>

```

Teraz wykonanie poleceń z filtrem **grep** spowoduje podświetlenie szukanej frazy:

```bash
karaf@root> list |grep Console
[  10] [Active     ] [Created     ] [       ] [   30] Apache Felix Karaf :: Shell Console (1.4.0.fuse-01-00)

```

Takie małe udogodnienie przy przeglądaniu dłuższych rezultatów jest nieocenione.

### Podstawowe polecenia konsoli

Polecenia w Apache Karaf są podzielone na kilka grup, które ułatwiają zarządzanie. Pierwszą grupą jest OSGi.

#### Polecenia OSGi

PoleceniePrzeznaczenielistWyświetlenie listy zainstalowanych paczekls \[bundle id\]Wyświetlenie usług eksportowanych przez paczkę.ls -u \[bundle id\]Wyświetlenie usług używanych przez paczkę.headers \[bundle id\]Wyświetlenie manifestu paczki.start \[bundle id\]Uruchomienie paczki o danym ID.stop \[bundle id\]Zatrzymanie paczki o danym ID.restart \[bundle id\]Zatrzymanie i wystartowanie paczki o danym ID.update \[bundle id\]Aktualizacja paczki.refresh \[bundle id\]Odświeżenie importów paczki a także przeładowanie kontekstu Spring-DM.install \[url\]Zainstalowanie nowej paczki.uninstall \[bundle id\]Odinstalowanie paczki.shutdownZatrzymanie kontenera.bundle-level \[bundle id\] \[startLevel\]Ustawienie start levelu dla paczki.start-level

Kiedy znamy już listę poleceń nie pozostaje nic innego jak je wypróbować. :-) Checkout [przykładowego kodu](http://svn.code-house.org/wjug/) z SVN pozwoli wykonać nam kilka ćwiczeń. Po wykonaniu polecenia **mvn clean install** w repozytorium Mavena są JARy które, naturalnie, chcemy zainstalować. Rezultat jaki powinniśmy zobaczyć w konsoli to:

```bash
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO] ------------------------------------------------------------------------
[INFO] Money transfer ServiceMix example ..................... SUCCESS [2.028s]
[INFO] Money transfer :: API ................................. SUCCESS [3.182s]
[INFO] Money transfer :: POI bundle .......................... SUCCESS [7.504s]
[INFO] Money transfer :: Internal ............................ SUCCESS [0.093s]
[INFO] Money transfer :: Internal :: CSV ..................... SUCCESS [4.399s]
[INFO] Money transfer :: Internal :: XLS ..................... SUCCESS [2.823s]
[INFO] Money transfer :: Internal :: Mail .................... SUCCESS [2.309s]
[INFO] Money transfer :: Internal :: Splitter ................ SUCCESS [0.936s]
[INFO] Money transfer :: Internal :: Routes .................. SUCCESS [1:21.241s]
[INFO] Money transfer :: External ............................ SUCCESS [0.016s]
[INFO] Money transfer :: External :: Customer ................ SUCCESS [3.182s]
[INFO] Money transfer :: External :: Bank .................... SUCCESS [2.059s]
[INFO] Money transfer :: External :: Validator ............... SUCCESS [2.918s]
[INFO] Money transfer :: Features ............................ SUCCESS [0.171s]
[INFO] ------------------------------------------------------------------------
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1 minute 54 seconds
[INFO] Finished at: Mon Mar 22 10:47:27 CET 2010
[INFO] Final Memory: 127M/341M
[INFO] ------------------------------------------------------------------------

```

Może teraz kilka słów o modułach które są widoczne w Mavenie.
ModułŚcieżka  
Maven IDPrzeznaczenieMoney transfer :: APIapi  
org.code-house.samples/apiAPI systemów zewnętrznych oraz POJO wykorzystywane do komunikacji.Money transfer :: POI bundlepoi  
org.code-house.samples/poiBundle zawierający bibliotekę Apache POI oraz wszystkie jej zależności z Manifestem OSGi pozwalającym na jej uruchomienie na szynie.Money transfer :: Internal :: CSVinternal/csv  
org.code-house.samples.internal/csvPaczka zawierająca implementację procesora przetwarzającego pliki CSV na POJO MoneyTransfer.Money transfer :: Internal :: XLSinternal/xls  
org.code-house.samples.internal/xlsPaczka zawierająca implementację procesora przetwarzającego pliki XLS na POJO MoneyTransfer.Money transfer :: Internal :: Mailinternal/mail  
org.code-house.samples.internal/mailPaczka z kodem dzielącym przychodzący mail z załącznikami na pojedyncze wiadomości które można przetworzyć jako XLS bądź CSV.Money transfer :: Internal :: Splitterinternal/splitter  
org.code-house.samples.internal/splitterPaczka z kodem odpowiedzialnym za rozdzielanie listy obiektów MoneyTransfer na listę wiadomości.**Money transfer :: Internal :: Routes**internal/routes  
org.code-house.samples.internal/routes**Główna paczka z definicjami routingu**Money transfer :: External :: Customerexternal/customer  
org.code-house.samples.external/customerImplementacja WebService odpowiedzialnego za pobieranie danych klienta na podstawie numeru rachunku bankowego.Money transfer :: External :: Bankexternal/bank  
org.code-house.samples.external/bankImplementacja usługi zwracającej informację o nazwie banku na podstawie numeru rachunku.Money transfer :: External :: Validatorexternal/validator  
org.code-house.samples.external/validatorUsługa weryfikująca czy MoneyTransfer jest poprawny.Money transfer :: Featuresfeatures  
org.code-house.samples/featuresModuł zawierający opcjonalny deskryptor do uruchomienia modułów.

Jak widać dwa główne obszary projektu są skupione w katalogach internal oraz external. Ten pierwszy zawiera implementacje ściśle związaną z usługami natomiast drugi to "zatyczki" emulujące działanie systemów zewnętrznych. Drobna nota - kolumna Maven ID nie zawiera informacji o wersji - w każdym module jest to **1.0.0.SNAPSHOT**.

Aby zainstalować któryś bundle przechodzimy do konsoli ServiceMix'a i wykonujemy takie polecenia:

```bash
karaf@root> install -s mvn:org.code-house.samples/api/1.0.0.SNAPSHOT
Bundle ID: 210
karaf@root> features:install camel-jetty
karaf@root> install -s mvn:org.code-house.samples.external/customer/1.0.0.SNAPSHOT
Bundle ID: 211

```

Po wykonaniu tych poleceń powinien być uruchomiony Web Service którego WSDL znajduje się pod adresem http://localhost:9001/CustomerWs?wsdl. Przełącznik **-s** w przypadku polecenia install powoduje że po zainstalowaniu bundle zostanie wystartowany. Polecenie **features-install camel-jetty** jest potrzebne nie ze względu na to że kolejna paczka korzysta z Camela, dzięki jego wykonaniu zostanie zainstalowane Jetty, z którego korzysta CXF.

Kolejne paczki instalujemy analogicznie:

```bash
karaf@root> features:install camel-activemq
karaf@root> install -s mvn:org.code-house.samples.external/validator/1.0.0.SNAPSHOT
Bundle ID: 215
karaf@root> install -s mvn:org.code-house.samples.external/bank/1.0.0.SNAPSHOT
Bundle ID: 217

```

Rozszerzenie [camel-activemq](http://camel.apache.org/activemq.html) jest rozszerzeniem modułu [camel-jms](http://camel.apache.org/jms.html) które pozwala na nieco wydajniejszą pracę z [ActiveMQ](http://activemq.apache.org).

#### Integracja ServiceMix z repozytoriami Mavena

Maven jako narzędzie do budowania korzysta z określonego schematu składowania bibliotek które następnie są automatycznie pobierane. Karaf, który jak wspomniałem podczas prezentacji, wyłonił się z projektu ServiceMix Kernel korzysta z biblioteki [Pax URL](http://wiki.ops4j.org/display/paxurl/Documentation). Dzięki temu z marszu mamy dostęp do standardowych repozytoriów Mavena, co jednak gdy mamy swoje repozytorium, które zawiera tylko nasze artefakty?
Otwieramy plik etc/org.ops4j.pax.url.mvn.cfg i dodajemy w nim co trzeba. Moja standardowa konfiguracja wygląda następująco:
```properties
org.ops4j.pax.url.mvn.settings=E:/tools/maven-2.2.1/conf/settings.xml
org.ops4j.pax.url.mvn.localRepository=E:/repository
org.ops4j.pax.url.mvn.defaultRepositories=file:${karaf.home}/${karaf.default.repository}@snapshots
org.ops4j.pax.url.mvn.repositories=\
    http://repo1.maven.org/maven2,\
    http://repo.fusesource.com/maven2,\
    http://repo.fusesource.com/maven2-snapshot@snapshots@noreleases,\
    http://repository.apache.org/content/groups/snapshots-group@snapshots@noreleases,\
    http://repository.ops4j.org/maven2,\
    http://svn.apache.org/repos/asf/servicemix/m2-repo,\
    http://repository.springsource.com/maven/bundles/release,\
    http://repository.springsource.com/maven/bundles/external,\
    http://repository.code-house.org/content/groups/release,\
    http://repository.code-house.org/content/groups/snapshot@snapshots@noreleases,\
    http://jansi.fusesource.org/repo/release
```
Dzięki temu PAX w pierwszej kolejności będzie skanował katalog E:/repository zamiast standardowego ~/.m2/repository. Jeśli któreś z repozytoriów wymaga autoryzacji adres powinien wyglądać następująco:
```
http://user:pass@jansi.fusesource.org/repo/release
```

### Podsumowanie

Mam nadzieję że wpis ten przybliży chociaż w niewielkim stopniu ServiceMix 4 oraz Karafa. W przyszłym wpisie, którego daty publikacji nie sposób przewidzieć zostaną dokładniej omówione polecenia z grupy **features**. Póki co życzę miłej zabawy z konsolą. :-) W razie pytań, niejasności i problemów - proszę o komentarze.

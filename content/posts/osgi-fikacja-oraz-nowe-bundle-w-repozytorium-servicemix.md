---
author: splatch
category:
  - uncategorized
date: "2010-03-24T16:26:52+00:00"
guid: http://blog.code-house.org/?p=249
summary: W tym wpisie zostanie omówiony proces OSGi-fikacji artefaktów, który przechodziłem gdy uruchamiałem prostą usługę na ServiceMix, która miała śledzić zewnętrzny RSS i pobierać z niego wpisy. Postanowiłem skorzystać z camel-rss. Przykłady które były do niego załączone są wystarczające by stworzyć odpowiedniego konsumenta...
title: OSGi-fikacja oraz nowe bundle w repozytorium ServiceMix
url: /2010/03/osgi-new-bundles-servicemix-repository/

---
W tym wpisie zostanie omówiony proces OSGi-fikacji artefaktów, który przechodziłem gdy uruchamiałem prostą usługę na ServiceMix, która miała śledzić zewnętrzny RSS i pobierać z niego wpisy. Postanowiłem skorzystać z camel-rss. Przykłady które były do niego załączone są wystarczające by stworzyć odpowiedniego konsumenta...

Problem zaczął się gdy usiłowałem uruchomić endpoint camela w OSGi. Mimo poprawnej konfiguracji, rozwiązanych zależności otrzymywałem wyjątek:
\[code\]java.lang.NoClassDefFoundError: Could not initialize class com.sun.syndication.feed.synd.SyndFeedImpl
 at com.sun.syndication.io.SyndFeedInput.build(SyndFeedInput.java:123)
 at org.apache.camel.component.rss.RssUtils.createFeed(RssUtils.java:34)
 at org.apache.camel.component.rss.RssEntryPollingConsumer.createFeed(RssEntryPollingConsumer.java:54)
 at org.apache.camel.component.feed.FeedEntryPollingConsumer.poll(FeedEntryPollingConsumer.java:42)
 at org.apache.camel.impl.ScheduledPollConsumer.run(ScheduledPollConsumer.java:106)
 at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:441)
 at java.util.concurrent.FutureTask$Sync.innerRunAndReset(FutureTask.java:317)
 at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:150)
 at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$101(ScheduledThreadPoolExecutor.java:98)
 at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.runPeriodic(ScheduledThreadPoolExecutor.java:181)
 at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:205)
 at java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:886)
 at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:908)
 at java.lang.Thread.run(Thread.java:619)
\[/code\]
Naturalnie, strasznie zirytowany, wziąłem się za dochodzenie - początkowo byłem przekonany że brakuje importów w camel-rss jednakże krótkie googlowanie [wskazało rozwiązanie](http://js.jipiju.com/2009/08/04/osgi-jumping-through-classoading-hoops/). Winne było kilka linii w klasie PluginManager:
\[code language="java"\]
 private Class\[\] getClasses() throws ClassNotFoundException {
 // Ten ClassLoader wskazuje na bundle w którym jest zdefiniowany endpoint!
 ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
 List classes = new ArrayList();
 boolean useLoadClass = Boolean.valueOf(System.getProperty(&amp;quot;rome.pluginmanager.useloadclass&amp;quot;, &amp;quot;false&amp;quot;)).booleanValue();
 for (int i = 0; i &amp;lt;\_propertyValues.length; i++) {
 // Naturalnie tutaj leciał ClassNotFoundException
 Class mClass = (useLoadClass ? classLoader.loadClass(\_propertyValues\[i\]) :
 Class.forName(\_propertyValues\[i\], true, classLoader));
 classes.add(mClass);
 }
 Class\[\] array = new Class\[classes.size()\];
 classes.toArray(array);
 return array;
 }
\[/code\]

Po przeróbce metoda wygląda następująco.
\[code language="java"\]
 private Class\[\] getClasses() throws ClassNotFoundException {
 ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
 List classes = new ArrayList();
 boolean useLoadClass = Boolean.valueOf(System.getProperty(&amp;quot;rome.pluginmanager.useloadclass&amp;quot;, &amp;quot;false&amp;quot;)).booleanValue();
 for (int i = 0; i &amp;lt;\_propertyValues.length; i++) {
 Class mClass = null;
 try {
 if (useLoadClass) {
 mClass = classLoader.loadClass(\_propertyValues\[i\]);
 } else {
 mClass = Class.forName(\_propertyValues\[i\], true, classLoader);
 }
 } catch (ClassNotFoundException e) {
 // Jeśli zewnętrzny class loader zgłosi wyjątek usiłujemy załadować klasę
 // z bieżącej paczki
 mClass = getClass().getClassLoader().loadClass(\_propertyValues\[i\]);
 }
 classes.add(mClass);
 }
 Class\[\] array = new Class\[classes.size()\];
 classes.toArray(array);
 return array;
 }
\[/code\]

Naturalnie, można się zastanawiać po co bibliotece do obsługi RSS zabiegi z ClassLoaderami. Otóż ROME wykorzystuje plik .properties do konfiguracji "pluginów" (poors man DI). W określonych miejscach możemy dodać własne klasy które obsłużą jakiś niestandardowy format. Problem w tym, że "patent" z plikiem properties świetnie sprawdza się przy płaskim classloaderze, niestety zawodzi w OSGi. Należy pamiętać o tym, że w OSGi nasz class loader ma dostęp do tego, do czego mu pozwalają importy i nie wszystko to, co widzi nasze oko w archiwum musi być dostępne dla naszego programu.

Przy okazji stworzenia [bundle z ROME 1.0](https://issues.apache.org/activemq/browse/SMX4-510) postanowiłem również stworzyć bundle dla [Apache POI 3.6](https://issues.apache.org/activemq/browse/SMX4-511). Bibliotekę tą wykorzystywałem wspólnie z [Tomkiem Nurkiewiczem](http://nurkiewicz.blogspot.com/) podczas prezentacji "Mule ESB vs ServiceMix".

## OSGi-fikacja

Bardzo swobodna definicja:

> OSGi-fikacja to proces mający na celu stworzenie działającego bundle z istniejącej już biblioteki. Wiele z projektów nie dostarczają poprawnych z punktu widzenia frameworku OSGi manifestów, czasami są budowane poprzez Ant bądź w ogóle są dostępne tylko ich binarne wersje co utrudnia analizę. Proces ten w większości przypadków sprowadza się do analizy zależności klas (importów) oraz zadeklarowanych klas (eksportów). W skrajnych wypadkach konieczna jest dodanie kodu bądź podmiana jego fragmentów tak by nie powodowały problemów po uruchomieniu. W celu zachowania porządku w publicznych repozytoriach Mavena stworzone w ten sposób archiwa są zapisywane z innym ArtifactId bądź GroupId, natomiast z zachowaniem oryginalnej wersji.

Nie aspiruję do miana człowieka który tworzy nowe pojęcia. Ten nieco przydługi wywód ma na celu jedynie przybliżenie działań które czasami są konieczne do uzyskania biblioteki działającej w OSGi.

Na potrzeby przykładu OSGi-fikacji wybrałem [log4j](http://logging.apache.org/log4j/1.2/index.html), jako popularną bibliotekę z małym zbiorem zależności. Wersja 1.2.12 nie zawiera poprawnego manifestu OSGi przez co nie można jej użyć pod Equinoxem czy Felixem.
Zależności które ma log4j takie jak:

- javax.mail
- javax.swing
- javax.naming
- javax.activation
- javax.management
- com.sun.jdmk.comm

Są opcjonalne, co znaczy że biblioteka bez problemów uruchomi się jeśli nie uda się zaimportować wyżej wymienionych paczek. Określimy zatem ich **resolution** na _optional_.

Pozostałe zależności wymienione poniżej są wymagane by móc odczytać plik log4j.xml:

- org.w3c.dom
- javax.xml.parsers
- org.xml.sax

Poniżej znajduje się pom.xml który przygotowałem po to by zaprezentować użycie wcześniej wspomnianych pluginów. Pom ten ma skutkować stworzeniem artefaktu OSGi gotowego do uruchomienia pod Kara-fem.
\[code language="xml"\]
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4\_0\_0.xsd">
 <modelVersion>4.0.0</modelVersion>

 <parent>
 <groupId>org.apache.servicemix.bundles</groupId>
 <artifactId>bundles-pom</artifactId>
 <version>4</version>
 </parent>

 <groupId>org.apache.servicemix.bundles</groupId>
 <artifactId>org.apache.servicemix.bundles.log4j</artifactId>
 <version>1.2.12-SNAPSHOT</version>
 <packaging>bundle</packaging>
 <name>Apache ServiceMix Bundles: ${pkgArtifactId}-${pkgVersion}</name>
 <description>
 This bundle simply wraps ${pkgArtifactId}-${pkgVersion}.jar.
 </description>

 <properties>
 <!\-\- Zmienne dla maven-bundle-plugin -->
 <servicemix.osgi.export.pkg>
 org.apache.log4j\*;version=${pkgVersion}
 </servicemix.osgi.export.pkg>
 <servicemix.osgi.import.pkg>
 <!\-\- Zależności opcjonalne -->
 com.sun.jdmk.comm;resolution:=optional,
 javax.jms;resolution:=optional,
 javax.mail\*;resolution:=optional,
 javax.management;resolution:=optional,
 javax.naming;resolution:=optional,
 javax.swing\*;resolution:=optional,
 \\* <!\-\- Wszystkie inne zależności jakie doda analizator -->
 </servicemix.osgi.import.pkg>
 <!\-\- Zmienne artefaktu -->
 <pkgGroupId>log4j</pkgGroupId>
 <pkgArtifactId>log4j</pkgArtifactId>
 <pkgVersion>1.2.12</pkgVersion>
 </properties>

 <dependencies>
 <!\-\- zależność do log4j -->
 <dependency>
 <groupId>${pkgGroupId}</groupId>
 <artifactId>${pkgArtifactId}</artifactId>
 <version>${pkgVersion}</version>
 <optional>true</optional>
 </dependency>
 </dependencies>

 <build>
 <plugins>
 <!\-\- Kopiowanie plików .class -->
 <plugin>
 <groupId>org.apache.maven.plugins</groupId>
 <artifactId>maven-shade-plugin</artifactId>
 <executions>
 <execution>
 <phase>package</phase>
 <goals>
 <goal>shade</goal>
 </goals>
 <configuration>
 <artifactSet>
 <includes>
 <include>${pkgGroupId}:${pkgArtifactId}</include>
 </includes>
 </artifactSet>
 <filters>
 <filter>
 <artifact>${pkgGroupId}:${pkgArtifactId}</artifact>
 <excludes>
 <exclude>\*\*</exclude>
 </excludes>
 </filter>
 </filters>
 <promoteTransitiveDependencies>true</promoteTransitiveDependencies>
 <createDependencyReducedPom>true</createDependencyReducedPom>
 </configuration>
 </execution>
 </executions>
 </plugin>
 </plugins>
 </build>
</project>
\[/code\]

Teraz pora na instalację bundla na szynie:

```

 ____                  _          __  __ _
/ ___|  ___ _ ____   _(_) ___ ___|  /  (_)_  __
___  / _  '__  / / |/ __/ _  |/| |  / /
 ___) |  __/ |    V /| | (_|  __/ |  | | |>  <
|____/ ___|_|    _/ |_|______|_|  |_|_/_/_

  Apache ServiceMix (4.2.0-fuse-01-00)

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
karaf@root>

karaf@root> install mvn:org.apache.servicemix.bundles/org.apache.servicemix.bundles.log4j/1.2.12-SNAPSHOT
Bundle ID: 186
karaf@root> list|grep log4j
[ 186] [Resolved   ] [            ] [       ] [   60] Apache ServiceMix Bundles: log4j-1.2.12 (1.2.12.SNAPSHOT)
karaf@root> start 186
karaf@root> list|grep log4j
[ 186] [Active     ] [            ] [       ] [   60] Apache ServiceMix Bundles: log4j-1.2.12 (1.2.12.SNAPSHOT)
karaf@root> packages:imports 186
OSGi System Bundle (0): javax.management; version="0.0.0"
OSGi System Bundle (0): javax.naming; version="0.0.0"
OSGi System Bundle (0): javax.swing; version="0.0.0"
OSGi System Bundle (0): javax.swing.border; version="0.0.0"
OSGi System Bundle (0): javax.swing.event; version="0.0.0"
OSGi System Bundle (0): javax.swing.table; version="0.0.0"
OSGi System Bundle (0): javax.swing.text; version="0.0.0"
OSGi System Bundle (0): javax.swing.tree; version="0.0.0"
OSGi System Bundle (0): javax.xml.parsers; version="0.0.0"
OSGi System Bundle (0): org.w3c.dom; version="0.0.0"
OSGi System Bundle (0): org.xml.sax; version="0.0.0"
OSGi System Bundle (0): org.xml.sax.helpers; version="0.0.0"
OPS4J Pax Logging - API (3): org.apache.log4j.spi; version="1.2.15"
OPS4J Pax Logging - API (3): org.apache.log4j.xml; version="1.2.15"
OPS4J Pax Logging - API (3): org.apache.log4j; version="1.2.15"
geronimo-jms_1.1_spec (64): javax.jms; version="1.1.0"
Apache ServiceMix Bundles: mail-1.4.1 (86): javax.mail.internet; version="1.4.1"
Apache ServiceMix Bundles: mail-1.4.1 (86): javax.mail; version="1.4.1"
karaf@root> packages:exports 186
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5.util; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.net; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5.viewer; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.jmx; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.jdbc; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.config; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.helpers; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5.config; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.or.jms; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.nt; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.or.sax; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5.viewer.categoryexplorer; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.or; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.chainsaw; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.varia; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5.viewer.configure; version="1.2.12"
Apache ServiceMix Bundles: log4j-1.2.12 (186): org.apache.log4j.lf5.viewer.images; version="1.2.12"
karaf@root>

```

Jak widać wszystko działa i ma się dobrze. :) Jedyna uwaga jaką mam to by nie używać tak spreparowanego log4j. W specyfikacji OSGi R4 V4.2 Enterprise jest opisana usługa logująca. Oprócz niej jest jeszcze [Pax Logging](http://wiki.ops4j.org/display/paxlogging/Pax+Logging) (używany przez Karafa) wspierająca kilka różnych bibliotek od log4j poprzez slf4j po wspomnianą usługę OSGi.

Pozdrawiam i życzę miłej OSGi-fikacji! :)

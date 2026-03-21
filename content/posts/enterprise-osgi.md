---
author: splatch
category:
  - uncategorized
date: "2010-03-23T12:26:18+00:00"
guid: http://blog.code-house.org/?p=233
title: Enterprise OSGi
url: /2010/03/enterprise-osgi/

---
Do opublikowania tego postu zachęcił mnie [Jacek Laskowski](http://jlaskowski.blogspot.com/) swym postem pod tytułem [W piątek 4Developers ze mną z Enterprise OSGi i in](http://jlaskowski.blogspot.com/2010/03/w-piatek-4developers-ze-mna-z.html).

Bardzo się cieszę że na 4Developers (na którym niestety mnie nie będzie) temat Enterprise OSGi będzie poruszony, ponieważ jak się zdaje jest to nieuchronny kierunek rozwoju Javy. Pod wpływem słów Jacka zacząłem się zastanawiać nad długofalowymi efektami jakie OSGi ma wnieść do developmentu.

Hałas który obecnie jest wokół OSGi w przybiera konkretne kształty w postaci projektów takich jak [Aries](http://incubator.apache.org/aries/) czy [Gemini](http://www.eclipse.org/gemini/). Obydwa projekty skupiają się nad ostatnimi draftami OSGi R4 V4.2 i mają na celu udostępnienie technologii takich jak JNDI, JPA i JMX wewnątrz kontenerów OSGi. Zacznijmy jednak od początku..

## OSGi a jarhell

> JAR-hell occurs when software is deployed into a runtime environment which is unsuitable, but nothing other than full integration testing would detect this. Having multiple software packages dependent upon the same piece of software, with unpredictable incompatibilities, is pure hell. Ensuring the compatibility of a variety of dependent packages is duanting, doing it amongst the variety supported by a hierarchy of complex class loaders, is inhuman.
>
> Źródło [Apache Depot](http://incubator.apache.org/depot/version/jar-hell.html)

Czyli w skrócie - piekło zaczyna się robić gdy pojawiają się niekompatybilności między bibliotekami w poprawnym środowisku. Co więcej owe niekompatybilności można wykryć dopiero po dogłębnych testach we wszystkich środowiskach w których ma działać aplikacja.

W przypadku OSGi wszystkie zależności są przewidywalne, co więcej nie uda się nam uruchomić paczki bez jej zależności - stąd teoretycznie nigdy nie powinniśmy widzieć ClassNotFoundException. Nie uda nam się również uruchomić naszego bundle jeśli powstanie konflikt w używanych zależnościach. Przykład z życia wzięty - mamy bundle zależące od camel-activemq oraz activemq-core. Pierwszy z nich pozwala na import spring-jms w wersji < 4.0, natomiast drugi w wersji < 2.6. Jeśli do tego mamy dwie wersje bundle spring-jms: 2.5.6 oraz 3.0.0 to mamy klapę. Naszej paczki nie da się wystartować ponieważ otrzymamy "Packages usage conflict". Zostaliśmy ochronieni przed JAR Hellem kosztem zablokowania kodu nawet jeśli zależność była opcjonalna.

### Rozwiązanie zagadki packages usage.

Problem wydaje się trywialny - teoretycznie to są dwie różne wersje Springa, nie da się zaprzeczyć że 2.5.6 != 3.0.0. W praktyce jednak zmiany w spring-jms były tak niewielkie że można bez problemu uruchomić activemq-core z nową wersją. W takim wypadku jesteśmy zmuszeni do czekania na nową wersję ActiveMQ, która będzie pozwalała na korzystanie ze Springa 3.0 bądź samodzielnie zmodyfikować manifesty. Obydwa rozwiązania są równie złe - jedno to czekanie, drugie to tworzenie nowej dystrybucji ActiveMQ.

Co w takim wypadku możemy zrobić? Możemy użyć serwisów OSGi, które pozwalają na oddzielenie implementacji od interfejsu, dzięki czemu możemy połączyć dwie wersje bibliotek za fasadą w postaci [ServiceReference](http://www.osgi.org/javadoc/r4v42/org/osgi/framework/ServiceReference.html). Tutaj jednak może pojawić się inny problem - mianowicie część bibliotek które lubią dostęp do ClassLoaderów może skutecznie protestować - na przykład Hibernate czy Open JPA. Dla przykładu diagram obrazujący kolejny z życia wzięty przypadek:

![Diagram bundli](http://blog.code-house.org/wp-content/uploads/bundle2.png)

W tym przypadku usiłowałem stworzyć działającą usługę która zapisywała by przychodzące komunikaty w bazie danych. Może parę słów o tym, który bundle co robi:

- **datasource** otwiera połączenie do bazy danych, tworzy ConnectionFactory dla JMS a także EntityManagera.
- **binding** bramka do przyjmowania komunikatów - w tym przypadku był to web service.
- **engine** definicja routingu z użyciem Camela.
- **persistence** użycie EntityManagera do zapisywania komunikatów
- **domain** POJO, klasy domenowe

Całość komunikacji odbywała się z JMS w trybie request-reply dzięki Apache Camel. Po bardzo długich "walkach" poniższą strukturę udało się uruchomić pod OSGi. Ostatecznie całość działa z Hibernate w układzie takim jak poniżej.

![Działająca struktura](http://blog.code-house.org/wp-content/uploads/bundle2-final.png)

Z diagramu wyrzuciłem paczki które nie są istotne takie jak driver JDBC czy Commons ConnectionPool. Jedyny mankament na jaki trafiłem wiąże się z **DAO Service**, mianowicie bundle który go eksportuje poprzez Spring-DM musi zadeklarować widoczność wszystkich swoich klas dla paczki która będzie korzystać z usługi co jak by nie patrzeć jest drobnym wypaczeniem fasady jaką ma być ServiceReference. Niestety po 2 tygodniach poświęconych na uruchomienie JPA w OSGi nie siliłem się na elegancję.

\[code language="xml"\]
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:osgi="http://www.springframework.org/schema/osgi"
 xsi:schemaLocation="
 http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans.xsd
 http://www.springframework.org/schema/osgi
 http://www.springframework.org/schema/osgi/spring-osgi.xsd
 ">

 <osgi:reference id="dataSource" interface="javax.sql.DataSource" />

 <osgi:service id="exchangeDAOExporter" ref="exchangeDAO"
 context-class-loader="service-provider"
 interface="org.code\_house.dataaccess.ExchangeDAO" />
</beans>
\[/code\]

## Kolejne metadane

Problem jaki powstaje z OSGi to metadane. Do tej pory - jeśli zarządzałem zależnościami do bibliotek robiłem to przez dependencyManagement Mavena, dzięki któremu rozwiązywałem wszystkie konflikty. OSGi jednak nie wiąże się z Mavenem ponieważ są to dwa różne obszary o zgoła innym funkcjonowaniu - OSGi to runtime, Maven to build time. Dopóki nie zapanuje harmonia pomiędzy tymi dwoma uruchamianie czegokolwiek w OSGi będzie katorgą. Należy do tego dodać jeszcze jeden element związany z OSGi - mianowicie **O** SGi **B** undle **R** epository (OBR), a OBR nijak się ma do repozytoriów Mavena przez co rozbieżności tylko się nasilają.
Aby temu zapobiegać najpopularniejsze istniejące repozytoria [SpringSource Enterprise Bundle Repository](http://www.springsource.com/repository/app/) oraz [ServiceMix 4 Bundles repository](http://servicemix.apache.org/SMX4/bundles-repository.html) \- publikują artefakty w repozytoriach Mavena. Problem w tym, że część artefaktów jest powielona. Tak jak kiedyś były 3 wersje Java Persistence API tak teraz dochodzą kolejne dwie - z manifestami OSGi. Czy ktoś wspominał o piekle?
Należy również dodać że nie każdy JAR który ląduje w OSGi jest traktowany tak samo - oprócz standardowych bundli są również fragmenty, które są świetnym rozwiązaniem, jednakże początkowo potrafią przysporzyć wielu problemów. Cały trik sprowadza się do tego, że fragmenty mają wspólny class loader z paczką do której są przypięte.

## Korzyści z Enterprise OSGi

![Vendor Lock](http://blog.code-house.org/wp-content/uploads/vendor-lock.png) Po całych tych wywodach na temat problemów z OSGi pora na to co ma ono nam dać - przenośność. Podobnie jak Java EE 6 z profilami tak OSGi ma zapewnić większą przenośność klocków pomiędzy środowiskami. Wyobrażacie sobie, że można przenieść aplikację z kontenera servletów na serwer aplikacyjny bez modyfikacji? Albo usługę z szyny integracyjnej na kontener servletów? Niedorzeczne, ale z OSGi możliwe do wykonania. Wystarczy zainstalować wszystkie wymagane bundle i całość będzie działać.

Oczywiście naiwna była by wiara w to, że tak będzie. Każda specyfikacja która powstaje dla Javy ma standaryzować i ujednolicać środowiska. W praktyce jednak każda z nich staje się punktem wyjściowym do rozwoju nowych produktów. Każdy dostawca oferuje zgodność ze specyfikacją plus coś. Nie twierdzę, że to złe, ponieważ polaryzacja rynku oprogramowania jest tak samo potrzebna jak wolny rynek, należy się jednak wystrzegać monopolistów a w przypadku oprogramowania również vendor locków.

Patrzę na OSGi z nadzieją ponieważ w moim mniemaniu jest to przedłużenie idei jaką niosła specyfikacja **J** ava **B** usiness **I** ntegration. [Propozycja JBI 2.0](http://jcp.org/en/jsr/results?id=4206) spotkała się z krytyką ze strony IBM oraz BEA Systems zasłaniających się tym, że istnieje SCA. Problem w tym, że obydwie specyfikacje traktują o innych warstwach integracji - SCA gwarantuje przenośność usług, podczas gdy JBI miało zapewnić przenośność komponentów i komunikacji między nimi. SCA i JBI mogą a nawet powinny iść w parze. Teoria JBI mówiła o możliwości uruchomienia servicemix-cxf-bc (zgodnego z JBI) na Open ESB, w praktyce okazywało się to jednak bardzo trudne. Dzięki OSGi/Enterprise OSGi stanie się to łatwiejsze.

W ciągu kilku najbliższych lat trend OSGi dzięki zainteresowaniu wielkich korporacji będzie rósł w siłę. Trzymam kciuki by Enterprise OSGI przyniosło więcej korzyści niż tylko zbawienie od JAR Hella.

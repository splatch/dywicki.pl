---
author: splatch
category:
  - uncategorized
date: "2008-07-23T08:27:55+00:00"
guid: http://blog.dywicki.pl/?p=219
summary: |-
  Od jakiegoś czasu w pracy do tworzenia usług sieciowych korzystam z [Apache CXF](http://cxf.apache.org). Jako że biblioteka jest stosunkowo nowa i nie najlepiej udokumentowana postanowiłem przedstawić na blogu jak wygląda proces tworzenia.

  CXF jest połączeniem kilku bibliotek - [YOKO](http://cwiki.apache.org/YOKO/), [Celtixa](http://celtix.objectweb.org/) oraz [XFire](http://xfire.codehaus.org/). Każda z nich wcześniej realizowała pewien fragment obecnej funkcjonalności CXF - YOKO obsługuje Corbę a XFire usługi sieciowe. Obecne CXF jest gotowy do używania "produkcyjnego", ponieważ niedawno wyszedł z fazy inkubacji. :)
title: Budowanie usługi sieciowej w oparciu o Apache CXF
url: /2008/07/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/

---
Od jakiegoś czasu w pracy do tworzenia usług sieciowych korzystam z [Apache CXF](http://cxf.apache.org). Jako że biblioteka jest stosunkowo nowa i nie najlepiej udokumentowana postanowiłem przedstawić na blogu jak wygląda proces tworzenia.

CXF jest połączeniem kilku bibliotek - [YOKO](http://cwiki.apache.org/YOKO/), [Celtixa](http://celtix.objectweb.org/) oraz [XFire](http://xfire.codehaus.org/). Każda z nich wcześniej realizowała pewien fragment obecnej funkcjonalności CXF - YOKO obsługuje Corbę a XFire usługi sieciowe. Obecne CXF jest gotowy do używania "produkcyjnego", ponieważ niedawno wyszedł z fazy inkubacji. :)

### Architektura

CXF ma dosyć elastyczną budowę. Zgodnie z [dokumentacją](http://cwiki.apache.org/CXF20DOC/cxf-architecture.html) można wyróżnić najważniejsze składowe:

- **Bus**, jest trzonem architektury CXF w którym definiuje i konfiguruje się rozszerzenia.
- **Messaging & Interceptors**, zapewniają niskopoziomowy dostęp do komunikatów oraz warstwę na której jest oparta większość funkcjonalności.
- **Front ends**, frontendy są interfejsami programistycznymi do tworzenia usług (np. JAX-WS).
- **Services**, usługi zapewniają model wraz z opisem
- **Bidings**, element ten jest odpowiedzialny za obsługę konkretnego protokołu (SOAP, REST, Corba etc).
- **Transports**, warstwa abstrakcji ułatwiająca zmianę sposobu transportu do/z usług.

### Markieting :)

CXF oferuje infrastrukturę konieczną do budowania usług, z najważniejszy zalet można wymienić:

- Wsparcie dla różnych protokołów.
- Obsługa standardów WS-\*, tj. WS-Addressing, WS-Security, WS-ReliableMessaging, oraz WS-Policy.
- Obsługa wielu transportów.
- Dołączane data-bindingi (np JAXB, Aegis).
- Jasny podział front endów takich jak JAX-WS od najważniejszego kodu.
- Wysoka wydajność.
- Możliwość osadzania w różnych środowiskach.

Z dodatkowych zalet, mogę dodać - bardzo łatwą integrację ze Springiem.

## Pierwsza usługa

Do budowania projektów będziemy używać [Mavena](http://maven.apache.org). Implementowana usługa będzie oparta o frontend JAX-WS z ręcznie pisanym deskryptorem usługi (WSDL first). Jakkolwiek w bardzo prosty sposób można odwrócić kolejność i przy pomocy pluginu CXF do Mavena wygenerować deskryptor.

Struktura projektów będzie następująca:

- parent  

   Rodzic projektu ze zdefiniowanymi wersjami bibliotek i raportami.

  - contract  

     Definicje używane zarówno przez klienta jak i serwer - WSDL oraz konfiguracja pluginu CXF.
  - client  

     Prosta biblioteka kliencka oparta o mechanizmy CXFa (JaxWSProxyFactoryBean).
  - server  

     Przykładowa implementacja usługi z bardzo prostym wykorzystaniem Springa.
  - webapp  

     Konfiguracja transporty CXF - w tym konkretnym przypadku servletu CXF.

## Parent

Poniżej znajduje się deskryptor projektu, który jest używany do budowania całości.
```xml
4.0.0org.code-house.cxfparentCode House.Org - CXF1.0-SNAPSHOTpomRodzic projektu, zawiera wszystkie moduly.cxf-clientcxf-contractcxf-servercxf-webapp2.1.12.1.32.5.4maven-compiler-plugin1.5
 1.5org.apache.cxfcxf-rt-frontend-jaxws${code-house.cxf.version}org.apache.cxfcxf-rt-transports-http${code-house.cxf.version}org.apache.cxfcxf-rt-transports-http-jetty${code-house.cxf.version}com.sun.xml.bindjaxb-impl${code-house.jaxb.version}javax.xml.bindjaxb-api2.1
```

## Contract

Zgodnie z tym, co napisałem wcześniej - przyjąłem podejście, że WSDL jest pisany ręcznie, głównie dlatego że dla większych projektów można w prosty sposób narzucić jakąś organizację i podział plików, z których są następnie generowane źródła.  

Najistotniejsza wstawka, która powinna znaleźć się w pomie:
```xml
org.apache.cxfcxf-codegen-plugingenerate-sources${basedir}/target/jaxws
 ${basedir}/src/main/resources/maven.wsdl
 -quietwsdl2java
```

Po dodaniu tej wstawki do sekcji **build/plugins** możemy przejść do tworzenia deskryptora usługi. W moim przypadku przyjąłem następujący podział:

1. maven.wsdl - definicje metod oraz komunikatów w rozumieniu WSDL (messages). Można z powodzeniem wyłączyć z tego pliku same koperty i pozostawić metody a to co potrzebne włączyć dyrektywą **wsdl:import**.
1. types.xsd - typy używane do komunikacji - zazwyczaj pary request+response używane bezpośrednio w definiowaniu elementów **wsdl:part**.
1. definition.xsd - definicje typów złożonych, niezależnych od usług, tj. opis domain-modelu z którym usługa pracuje.

Każdy z tych plików ma inną przestrzeń nazw.

### maven.wsdl

```xml

```

### types.xsd

```xml

```

### definition.xsd

```xml

```

Po odpaleniu polecenia **mvn:install** powinniśmy otrzymać w konsoli fragment podobny do tego:
```
[INFO] [cxf-codegen:wsdl2java {execution: default}]
```
Jest to informacja, że plugin CXF został poprawnie skonfigurowany i uruchomiony.

## Serwer

Sercem naszej usługi jest oczywiście jej implementacja dlatego też nie możemy obejść się bez niej. :) Projekt ten ma tylko dwie zależności - **contract** oraz artefakt **cxf-rt-frontend-jaxws**.

Konfiguracja serwera odbywa się w oparciu o springa:
```xml

```

Serwer zawiera w zasadzie niewiele kodu, oto i on:
```java
package org.code_house.services.maven;

import java.net.URISyntaxException;

import javax.jws.WebService;

import org.code_house.services.maven.definition.ArtifactInfo;
import org.code_house.services.maven.types.FindArtifactRequest;
import org.code_house.services.maven.types.FindArtifactRespose;

/**
 * Implementacja usługi.
 */
@WebService(serviceName = "MavenService",
 endpointInterface = "org.code_house.services.maven.MavenArtifactType",
 targetNamespace = "http://code-house.org/services/maven"
)
public class MavenArtifactTypeImpl implements MavenArtifactType {

 /**
 * Bean zawierający implementację logiki biznesowej.
 */
 private MavenSearchService service;

 public FindArtifactRespose findArtifact(FindArtifactRequest request) {
 ArtifactInfo info = request.getQuery(); // pobranie struktury przekazanej od klienta

 // sformuowanie odpowiedzi
 FindArtifactRespose response = new FindArtifactRespose();
 try {
 response.setDownloadURL(service.find(info).toURI().toString());
 } catch (URISyntaxException e) {
 throw new RuntimeException(e);
 }
 return response;
 }

 /**
 * Ustawienie wartości pola service.
 *
 * @param service Nowa wartość pola service.
 */
 public void setService(MavenSearchService service) {
 this.service = service;
 }
}
```

Dodatkowy kod, który nie jest konieczny do implementacji usługi to definicja interfejsu **MavenSearchService**. Dzięki zastosowaniu takiego rozwiązania można w prostszy sposób testować działanie usługi poprzez przekazywanie jej mocka stworzonego np. przy pomocy [easy mocka](http://easymock.org). Kod takiego testu pominąłem ze względu na to, że notka i tak już jest za długa w tym momencie a jesteśmy ledwo w połowie drogi. :)

Przykładowa implementacja wcześniej wspomnianego intefejsu nie zawiera żadnej logiki i zawsze zwraca tą samą wartość.
```java
package org.code_house.services.maven;

import java.net.MalformedURLException;
import java.net.URL;

import org.code_house.services.maven.definition.ArtifactInfo;

// Najprostsza implementacja tylko po to żeby sprawdzić działanie usługi
public class DummyMavenSearchServiceImpl implements MavenSearchService {

 public URL find(ArtifactInfo info) {
 try {
 return new URL("http://repo1.maven.org/maven2/org/apache/apache/4/apache-4.pom");
 } catch (MalformedURLException e) {
 throw new RuntimeException(e);
 }
 }

}
```

Zanim odpalimy serwer konieczna będzie jeszcze jedna rzecz - konfiguracja transporu, w naszym przypadku servletu CXF.

## Webapp

Webapp posiada bezpośrednie zależności do 2 artefaktów CXFa: **cxf-rt-transports-http** oraz **cxf-rt-bindings-http**. Druga jest opcjonalna, bez niej CXF nie będzie wyświetlał dostępnych usług w postaci tabelki html.

Przydatna może być wtyczka jetty (sekcja **build/plugins**), która pozwala na uruchomienie aplikacji bez konieczności instalowania kontenera servletów:
```xml
org.mortbay.jettymaven-jetty-plugin6.1.1110808060000
```

Ostatnia zależność odnosi się do implementacji usługi - czyli naszego artefaktu **server**. Najistotniejsze elementy konfiguracji webappa to web.xml oraz cxf-beans.xml.

### web.xml

Warto zauważyć w poniższym listingu fajną możliwość, którą daje nam Spring w postaci wildcardów określających położenie konfiguracji - w tym przypadku **classpath:/module/\*-context.xml**. Oznacza to, że Spring przeskanuje wszystkie biblioteki od których zależy projekt i dołączy ich konfigurację do głownej, dzięki czemu będziemy mogli uzyskać bardziej modułową i elastyczną budowę aplikacji. Może [Dynamic Modules](http://www.springframework.org/osgi) to nie jest ale na codzień w zupełności wystarcza :).

```xml
Maven Search Services :: WebappFrontend indeksera.contextConfigLocation
 /WEB-INF/security.xml

 /WEB-INF/cxf-beans.xml

 /WEB-INF/applicationContext.xml

 classpath:/module/*-context.xml
 CXFServletorg.apache.cxf.transport.servlet.CXFServlet1CXFServlet/services/*org.springframework.web.context.ContextLoaderListener
```

### cxf-beans.xml

Plik ten zawiera definicje konfiguracyjne CXFa. W poniższej formie włączana jest tylko część modułów CXF w celu zmniejszenia użycia zasobów.
```xml

```

Pozostałe pliki konfiguracyjne są praktycznie puste, ponieważ tyczą się obszarów niezwiązanych z tematem tego wpisu. Po tym wszystkim możemy uruchomić naszą aplikację poleceniem **mvn jetty:run**. Po wejściu na adres http://localhost:8080/webapp/services naszym oczom powinna ukazać się lista podobna do poniższej:
![services](/wp-content/uploads/2008/07/services.jpg)
Nie wiem niestety dlaczego CXF generuje zły adres usługi (pomijający mapowanie servletu) oraz ma problemy z wystawieniem WSDLa. Jakkolwiek i bez tego wszystko działa poprawnie - znaczy wywoływanie usług rzecz jasna. :)

[![soap-ui](/wp-content/uploads/2008/07/soap-ui-300x182.jpg)](/wp-content/uploads/2008/07/soap-ui.jpg) W tym momencie możemy uruchomić [Soap UI](http://www.soapui.org/) i wykonać jedyną dostępną metodę - findArtifact.

## Client

Ostatnim z elementów, który pozostał do omówienia jest klient. Niestety z racji na to, że ta nota już się wystarczająco rozrosła zrobię to w kolejnym wpisie. Mam nadzieję, że nota ta nieco ułatwi przyszłym użytkownikom CXFa jego poznanie. :) Dla tych, którym nie chce się wpisywać tego wszystkiego w edytorze [zamieszczam źródła](http://media.dywicki.pl/blog/cxf/cxf.zip) razem z działającym klientem. Projekty nie są nadzwyczajnie dopieszczone, ale w zupełności wystarczą do startu. Znajduje się też tam w pełni działający klient stworzonej usługi. Pozdrawiam i życzę miłej zabawy!

---
author: splatch
category:
  - uncategorized
date: "2008-09-03T09:28:27+00:00"
guid: http://blog.dywicki.pl/?p=223
summary: |-
  W nawiązaniu do [poprzedniej noty o CXFie](http://blog.dywicki.pl/2008/07/23/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/), którą napisałem jakiś czas temu, gonię aby uzupełnić brak konfiguracji klienta. Sam proces jest bardzo zbliżony do tworzenia klienta w oparciu o XFire. Nie jest wymagana duża ilość kodu Javy, a w zasadzie tylko dwa pliki XML (client.xml, _myservice.xml_).

  Pierwszy z nich odpowiada za wczytanie wymaganych rozszerzeń CXFa oraz definicję bazowej konfiguracji fabryki z interceptorami. W interceptorach możemy skonfigurować logowanie, obsługę załączników czy standardów WS-Security etc. Wszystkie te ustawienia będą dziedziczone, a fabryki docelowych usług będą dodawać tylko adres, do odpytywania. Na koniec bean klienta będzie miał określony **autowire** by nie przekazywać mu wszystkich własności.
title: Budowanie klienta usługi sieciowej w oparciu o Apache CXF
url: /2008/09/budowanie-klienta-uslugi-sieciowej-w-oparciu-o-apache-cxf/

---
W nawiązaniu do [poprzedniej noty o CXFie](http://blog.dywicki.pl/2008/07/23/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/), którą napisałem jakiś czas temu, gonię aby uzupełnić brak konfiguracji klienta. Sam proces jest bardzo zbliżony do tworzenia klienta w oparciu o XFire. Nie jest wymagana duża ilość kodu Javy, a w zasadzie tylko dwa pliki XML (client.xml, _myservice.xml_).

Pierwszy z nich odpowiada za wczytanie wymaganych rozszerzeń CXFa oraz definicję bazowej konfiguracji fabryki z interceptorami. W interceptorach możemy skonfigurować logowanie, obsługę załączników czy standardów WS-Security etc. Wszystkie te ustawienia będą dziedziczone, a fabryki docelowych usług będą dodawać tylko adres, do odpytywania. Na koniec bean klienta będzie miał określony **autowire** by nie przekazywać mu wszystkich własności.

Oto najważniejsze wstawki kodu oraz ich opis:
```xml
org.code-house.cxfparent1.0-SNAPSHOT4.0.0org.code-house.cxfclient1.0-SNAPSHOTCode House.Org - CXF - Clientlog4jlog4j1.2.12org.code-house.cxfcontract1.0-SNAPSHOTorg.apache.cxfcxf-rt-frontend-jaxwsorg.apache.cxfcxf-rt-transports-httporg.apache.cxfcxf-rt-transports-http-jetty${code-house.cxf.version}org.springframeworkspring-core${code-house.spring.version}org.springframeworkspring-test${code-house.spring.version}testorg.springframeworkspring-beans${code-house.spring.version}org.springframeworkspring-core${code-house.spring.version}org.springframeworkspring-context${code-house.spring.version}
```

Deskryptor nie jest zbyt złożony, istotny jest tylko kawałek z kontraktem, który jak wskazuje nazwa jest definicją używanych typów:
```xml
org.code-house.cxfcontract1.0-SNAPSHOT
```

Teraz kolej na jedyną wstawkę Javy, która się pojawia w projekcie. Jest to zwykły bean, który będzie miał później wstrzykiwane obiekty pośredniczące w wywoływaniu usług.

```java
package org.code_house.cxf.client;

import org.code_house.services.maven.MavenArtifactType;

/**
 * Klient usług Code-House.
 *
 * @author Łukasz Dywicki [email](splatch@code-house.org)
 *
 * $Id$
 */
public class Client {

 /**
 * Usługa do obsługi wyszukiwania artefaktów Mavena.
 */
 private MavenArtifactType maven;

 /**
 * Pobranie wartości pola maven.
 *
 * @return Wartość maven.
 */
 public MavenArtifactType getMaven() {
 return maven;
 }

 /**
 * Ustawienie wartości pola maven.
 *
 * @param maven Nowa wartość pola maven.
 */
 public void setMaven(MavenArtifactType maven) {
 this.maven = maven;
 }
}
```

Resztę magii załatwia Spring:
```xml
```

Zgodnie ze wstawką w linii 20 konieczna jest jeszcze konfiguracja usługi. Sztuczka polega na użyciu części konfiguracji zdefiniowanej wcześniej - baseClientFactory.
```xml
```

Ustawienia, które mogą ulec zmianie, to znaczy użytkownik, hasło oraz adres usługi są wyodrębnione do pliku client.properties:
```properties
# Placeholdery dla kontekstow springa
# Adresy uslug
server.port = 8080
host = localhost
org.code_house.cxf.service.maven http://${host}:${server.port}/webapp/services/maven

# Autoryzacja
org.code_house.cxf.user
org.code_house.cxf.password
```

No i na koniec opcjonalny test, który odpytuje usługę:
```java
package org.code_house.cxf.client;
import org.code_house.services.maven.definition.ArtifactInfo;
import org.code_house.services.maven.types.FindArtifactRequest;
import org.code_house.services.maven.types.FindArtifactRespose;
import org.springframework.test.AbstractDependencyInjectionSpringContextTests;

/**
 * Proste wywołanie klasy klienta.
 *
 * @author Łukasz Dywicki [email](ldywicki@pocztowy.pl)
 *
 * $Id$
 */
public class MainTest extends AbstractDependencyInjectionSpringContextTests {

 /**
 * Wstrzyknięty klient.
 */
 private Client client;

 @Override
 protected String[] getConfigLocations() {
 return new String[] {"classpath:client.xml"};
 }

 public void testOne() {
 FindArtifactRequest request = new FindArtifactRequest();
 ArtifactInfo artifact = new ArtifactInfo();
 artifact.setGroupId("org.code_house.cxf");
 artifact.setArtifactId("contract");
 request.setQuery(artifact);

 FindArtifactRespose respose = client.getMaven().findArtifact(request);
 System.out.println(respose.getDownloadURL());
 }

 /**
 * Ustawienie wartości pola client.
 *
 * @param client Nowa wartość pola client.
 */
 public void setClient(Client client) {
 this.client = client;
 }

}
```

To by było na tyle. Cały działający kod projektu jest już zamieszczony przy [poprzedniej nocie](http://blog.dywicki.pl/2008/07/23/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/), paczka ze wszystkimi listingami [gotowa do pobrania](http://media.dywicki.pl/blog/cxf/cxf.zip).

Teraz chyba pora zacząć opisywać mechanizmy Springa. :)

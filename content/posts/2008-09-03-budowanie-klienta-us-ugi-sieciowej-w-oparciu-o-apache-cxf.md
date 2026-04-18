---
author: splatch
category:
  - cxf
  - framework
  - java
date: "2008-09-03T07:28:27+00:00"
guid: http://blog.dywicki.pl/?p=223
summary: |-
  W nawiązaniu do [poprzedniej noty o CXFie](http://blog.dywicki.pl/2008/07/23/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/), którą napisałem jakiś czas temu, gonię aby uzupełnić brak konfiguracji klienta. Sam proces jest bardzo zbliżony do tworzenia klienta w oparciu o XFire. Nie jest wymagana duża ilość kodu Javy, a w zasadzie tylko dwa pliki XML (client.xml, _myservice.xml_).

  Pierwszy z nich odpowiada za wczytanie wymaganych rozszerzeń CXFa oraz definicję bazowej konfiguracji fabryki z interceptorami. W interceptorach możemy skonfigurować logowanie, obsługę załączników czy standardów WS-Security etc. Wszystkie te ustawienia będą dziedziczone, a fabryki docelowych usług będą dodawać tylko adres, do odpytywania. Na koniec bean klienta będzie miał określony **autowire** by nie przekazywać mu wszystkich własności.
title: Budowanie klienta usługi sieciowej w oparciu o Apache CXF
url: /2008/09/03/budowanie-klienta-uslugi-sieciowej-w-oparciu-o-apache-cxf/

---
W nawiązaniu do [poprzedniej noty o CXFie](http://blog.dywicki.pl/2008/07/23/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/), którą napisałem jakiś czas temu, gonię aby uzupełnić brak konfiguracji klienta. Sam proces jest bardzo zbliżony do tworzenia klienta w oparciu o XFire. Nie jest wymagana duża ilość kodu Javy, a w zasadzie tylko dwa pliki XML (client.xml, _myservice.xml_).

Pierwszy z nich odpowiada za wczytanie wymaganych rozszerzeń CXFa oraz definicję bazowej konfiguracji fabryki z interceptorami. W interceptorach możemy skonfigurować logowanie, obsługę załączników czy standardów WS-Security etc. Wszystkie te ustawienia będą dziedziczone, a fabryki docelowych usług będą dodawać tylko adres, do odpytywania. Na koniec bean klienta będzie miał określony **autowire** by nie przekazywać mu wszystkich własności.

Oto najważniejsze wstawki kodu oraz ich opis:
```xml
<parent>
    <groupId>org.code-house.cxf</groupId>
    <artifactId>parent</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>
<groupId>org.code-house.cxf</groupId>
<artifactId>client</artifactId>
<version>1.0-SNAPSHOT</version>
<name>Code House.Org - CXF - Client</name>
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.12</version>
    </dependency>
    <dependency>
        <groupId>org.code-house.cxf</groupId>
        <artifactId>contract</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-rt-frontend-jaxws</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-rt-transports-http</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.cxf</groupId>
        <artifactId>cxf-rt-transports-http-jetty</artifactId>
        <version>${code-house.cxf.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${code-house.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>${code-house.spring.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>${code-house.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${code-house.spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${code-house.spring.version}</version>
    </dependency>
</dependencies>
```

Deskryptor nie jest zbyt złożony, istotny jest tylko kawałek z kontraktem, który jak wskazuje nazwa jest definicją używanych typów:
```xml
<dependency>
    <groupId>org.code-house.cxf</groupId>
    <artifactId>contract</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

Teraz kolej na jedyną wstawkę Javy, która się pojawia w projekcie. Jest to zwykły bean, który będzie miał później wstrzykiwane obiekty pośredniczące w wywoływaniu usług.

```java
package org.code_house.cxf.client;

import org.code_house.services.maven.MavenArtifactType;

/**
 * Klient usług Code-House.
 *
 * @author Łukasz Dywicki
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
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
    ">
    <!--  Konfiguracja placeholderów (znaczy wstawek ${}) -->
    <context:property-placeholder location="classpath:client.properties" />

    <!-- Importy rzeczy koniecznych do pracy CXF -->
    <import resource="classpath:META-INF/cxf/cxf.xml" />
    <import resource="classpath:META-INF/cxf/cxf-extension-soap.xml" />
    <import resource="classpath:META-INF/cxf/cxf-extension-http-jetty.xml" />

    <!-- Konfiguracja poszczególnych usług wstrzykiwanych do klienta -->
    <import resource="classpath:services/*.xml" />

    <!-- Bean zawierający referencje do wygenerowanych klientów usług -->
    <bean id="client" class="org.code_.housecxf.client.Client" autowire="autodetect" />

    <!-- Bazowa konfiguracja fabryk - obiektów tworzących stuby klientów w runtime -->
    <bean id="baseClientFactory" abstract="true"
        class="org.apache.cxf.jaxws.JaxWsProxyFactoryBean">
       <property name="username" value="${org.code_house.cxf.user}" />
        <property name="password" value="${org.code_house.cxf.password}" />
        <property name="inInterceptors">
            <list>
                <ref bean="logIn" />
            </list>
        </property>
        <property name="outInterceptors">
            <list>
                <ref bean="logOut" />
            </list>
        </property>
    </bean>

    <!-- Loggery dla CXF -->
    <bean id="logIn" class="org.apache.cxf.interceptor.LoggingInInterceptor" />
    <bean id="logOut" class="org.apache.cxf.interceptor.LoggingOutInterceptor" />

</beans>
```

Zgodnie ze wstawką w linii 20 konieczna jest jeszcze konfiguracja usługi. Sztuczka polega na użyciu części konfiguracji zdefiniowanej wcześniej - baseClientFactory.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
    ">

    <!-- Konfiguracja stuba dla usługi wyszukiwania artefaktów -->
    <bean id="mavenService" class="org.code_house.services.maven.MavenArtifactType"
        factory-bean="mavenServiceFactory" factory-method="create" />

    <!-- Fabryka usługi wyszukującej -->
    <bean id="mavenServiceFactory" parent="baseClientFactory">
        <!-- jedyne parametry jakich potrzebujemy -->
        <property name="serviceClass"
            value="org.code_house.services.maven.MavenArtifactType" />
        <property name="address" value="${org.code_house.cxf.service.maven}" />
    </bean>

</beans>
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
 * @author Łukasz Dywicki
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

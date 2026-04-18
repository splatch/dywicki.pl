---
author: splatch
category:
  - cxf
  - java
  - jaxb
  - xml
date: "2008-07-23T06:27:55+00:00"
guid: http://blog.dywicki.pl/?p=219
summary: |-
  Od jakiegoś czasu w pracy do tworzenia usług sieciowych korzystam z [Apache CXF](http://cxf.apache.org). Jako że biblioteka jest stosunkowo nowa i nie najlepiej udokumentowana postanowiłem przedstawić na blogu jak wygląda proces tworzenia.

  CXF jest połączeniem kilku bibliotek - [YOKO](http://cwiki.apache.org/YOKO/), [Celtixa](http://celtix.objectweb.org/) oraz [XFire](http://xfire.codehaus.org/). Każda z nich wcześniej realizowała pewien fragment obecnej funkcjonalności CXF - YOKO obsługuje Corbę a XFire usługi sieciowe. Obecne CXF jest gotowy do używania "produkcyjnego", ponieważ niedawno wyszedł z fazy inkubacji. :)
title: Budowanie usługi sieciowej w oparciu o Apache CXF
url: /2008/07/23/budowanie-uslugi-sieciowej-w-oparciu-o-apache-cxf/

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
- Obsługa standardów WS-*, tj. WS-Addressing, WS-Security, WS-ReliableMessaging, oraz WS-Policy.
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
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
 
    <!-- Informacja dla Mavena -->
    <modelVersion>4.0.0</modelVersion>
 
    <!-- Identyfikacja projektu -->
    <groupId>org.code-house.cxf</groupId>
    <artifactId>parent</artifactId>
    <name>Code House.Org - CXF</name>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
 
    <description>Rodzic projektu, zawiera wszystkie moduly.</description>
 
    <!-- Składowe projektu -->
    <modules>
        <module>cxf-client</module>
        <module>cxf-contract</module>
        <module>cxf-server</module>
        <module>cxf-webapp</module>
    </modules>
 
    <!-- Definicje zmiennych dostępne również w modułach -->
    <properties>
        <code-house.cxf.version>2.1.1</code-house.cxf.version>
        <code-house.jaxb.version>2.1.3</code-house.jaxb.version>
        <code-house.spring.version>2.5.4</code-house.spring.version>
    </properties>
 
    <build>
        <plugins>
            <!-- Konfiguracja kompilatora -->
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.5</source>
                    <target>1.5</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
 
    <reporting>
    <!--Wycięte <img src="http://dywicki.pl/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley">  -->
    </reporting>
 
    <!-- Predefiniowane wersje bibliotek -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.apache.cxf</groupId>
                <artifactId>cxf-rt-frontend-jaxws</artifactId>
                <version>${code-house.cxf.version}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.cxf</groupId>
                <artifactId>cxf-rt-transports-http</artifactId>
                <version>${code-house.cxf.version}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.cxf</groupId>
                <artifactId>cxf-rt-transports-http-jetty</artifactId>
                <version>${code-house.cxf.version}</version>
            </dependency>
            <dependency>
                <groupId>com.sun.xml.bind</groupId>
                <artifactId>jaxb-impl</artifactId>
                <version>${code-house.jaxb.version}</version>
            </dependency>
            <dependency>
                <groupId>javax.xml.bind</groupId>
                <artifactId>jaxb-api</artifactId>
                <version>2.1</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
 
</project>
```

## Contract

Zgodnie z tym, co napisałem wcześniej - przyjąłem podejście, że WSDL jest pisany ręcznie, głównie dlatego że dla większych projektów można w prosty sposób narzucić jakąś organizację i podział plików, z których są następnie generowane źródła.  

Najistotniejsza wstawka, która powinna znaleźć się w pomie:
```xml
<!-- Generowanie kodu z deskryptora WSDL -->
<plugin>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-codegen-plugin</artifactId>
    <executions>
        <execution>
            <phase>generate-sources</phase>
            <configuration>
                <sourceRoot>${basedir}/target/jaxws</sourceRoot>
                <wsdlOptions>
                    <wsdlOption>
                        <wsdl>
                            ${basedir}/src/main/resources/maven.wsdl
                        </wsdl>
                        <extraargs>
                            <extraarg>-quiet</extraarg>
                        </extraargs>
                    </wsdlOption>
                </wsdlOptions>
            </configuration>
            <goals>
                <goal>wsdl2java</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Po dodaniu tej wstawki do sekcji **build/plugins** możemy przejść do tworzenia deskryptora usługi. W moim przypadku przyjąłem następujący podział:

1. maven.wsdl - definicje metod oraz komunikatów w rozumieniu WSDL (messages). Można z powodzeniem wyłączyć z tego pliku same koperty i pozostawić metody a to co potrzebne włączyć dyrektywą **wsdl:import**.
1. types.xsd - typy używane do komunikacji - zazwyczaj pary request+response używane bezpośrednio w definiowaniu elementów **wsdl:part**.
1. definition.xsd - definicje typów złożonych, niezależnych od usług, tj. opis domain-modelu z którym usługa pracuje.

Każdy z tych plików ma inną przestrzeń nazw.

### maven.wsdl

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wsdl:definitions xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
    xmlns:tns="http://code-house.org/services/maven"
    xmlns:types="http://code-house.org/services/maven/types"
    xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    targetNamespace="http://code-house.org/services/maven"
    name="MavenServices"
    >
 
    <wsdl:types>
        <xsd:schema targetNamespace="http://code-house.org/services/maven/types">
            <xsd:include schemaLocation="types.xsd" />
        </xsd:schema>
    </wsdl:types>
 
    <wsdl:message name="findArtifactRequest">
        <wsdl:part name="request" type="types:FindArtifactRequest" />
    </wsdl:message>
    <wsdl:message name="findArtifactResponse">
        <wsdl:part name="response" type="types:FindArtifactRespose" />
    </wsdl:message>
 
    <wsdl:portType name="MavenArtifactType">
        <wsdl:operation name="findArtifact">
            <wsdl:input message="tns:findArtifactRequest" />
            <wsdl:output message="tns:findArtifactResponse" />
        </wsdl:operation>
    </wsdl:portType>
 
    <wsdl:binding name="MavenArtifactSOAP" type="tns:MavenArtifactType">
        <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http" />
        <wsdl:operation name="findArtifact">
            <wsdl:input>
                <soap:body use="literal" />
            </wsdl:input>
            <wsdl:output>
                <soap:body use="literal" />
            </wsdl:output>
        </wsdl:operation>
    </wsdl:binding>
 
    <wsdl:service name="MavenServices">
        <wsdl:port binding="tns:MavenArtifactSOAP" name="Maven Services">
            <soap:address location="http://localhost:8080/webapp/services/maven" />
        </wsdl:port>
    </wsdl:service>
</wsdl:definitions>
```

### types.xsd

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
    targetNamespace="http://code-house.org/services/maven/types"
    xmlns:tns="http://code-house.org/services/maven/types"
    xmlns:def="http://code-house.org/services/maven/definition"
    elementFormDefault="qualified">
 
    <import schemaLocation="definition.xsd"
        namespace="http://code-house.org/services/maven/definition" />
 
    <complexType name="FindArtifactRequest">
        <sequence>
            <element name="query" type="def:ArtifactInfo" />
        </sequence>
    </complexType>
 
    <complexType name="FindArtifactRespose">
        <sequence>
            <element name="downloadURL" type="anyURI" />
        </sequence>
    </complexType>
 
</schema>
```

### definition.xsd

```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://www.w3.org/2001/XMLSchema"
    targetNamespace="http://code-house.org/services/maven/definition"
    xmlns:tns="http://code-house.org/services/maven/definition"
    elementFormDefault="qualified">
 
    <complexType name="ArtifactInfo">
        <sequence>
            <element name="groupId" type="string" />
            <element name="artifactId" type="string" />
            <element name="version" type="string" minOccurs="0" />
            <element name="classifier" type="tns:Classifier" minOccurs="0" />
            <element name="packaging" type="tns:Packaging" minOccurs="0" />
            <element name="type" type="tns:Type" minOccurs="0" />
        </sequence>
    </complexType>
 
    <simpleType name="Type">
        <restriction base="string">
            <enumeration value="dll" />
            <enumeration value="so" />
        </restriction>
    </simpleType>
 
    <simpleType name="Classifier">
        <restriction base="string">
            <enumeration value="sources" />
            <enumeration value="javadoc" />
            <enumeration value="resources" />
        </restriction>
    </simpleType>
 
    <simpleType name="Packaging">
        <restriction base="string">
            <enumeration value="pom" />
            <enumeration value="jar" />
            <enumeration value="war" />
            <enumeration value="bundle" />
        </restriction>
    </simpleType>
 
</schema>
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
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:jaxws="http://cxf.apache.org/jaxws"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://cxf.apache.org/jaxws
        http://cxf.apache.org/schemas/jaxws.xsd
    ">
 
    <!-- Dodatkowy bean zawierający właściwą implementację logiki związanej z wyszukiwaniem -->
    <bean id="service" class="org.code_house.services.maven.DummyMavenSearchServiceImpl" />
 
    <!-- Bean będący implementacją usługi jako takiej - obsługujący wejście/wyjście -->
    <bean id="mavenService" class="org.code_house.services.maven.MavenArtifactTypeImpl">
        <property name="service" ref="service" />
    </bean>
 
    <!-- Konfiguracja endpointu CXFa -->
    <jaxws:endpoint address="maven" id="jaxwsMavenService" implementor="#mavenService" />
 
</beans>
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
<plugin>
    <groupId>org.mortbay.jetty</groupId>
    <artifactId>maven-jetty-plugin</artifactId>
    <version>6.1.11</version>
    <configuration>
        <scanIntervalSeconds>10</scanIntervalSeconds>
        <connectors>
            <connector implementation="org.mortbay.jetty.nio.SelectChannelConnector">
                <port>8080</port>
                <maxIdleTime>60000</maxIdleTime>
            </connector>
        </connectors>
    </configuration>
</plugin>
```

Ostatnia zależność odnosi się do implementacji usługi - czyli naszego artefaktu **server**. Najistotniejsze elementy konfiguracji webappa to web.xml oraz cxf-beans.xml.

### web.xml

Warto zauważyć w poniższym listingu fajną możliwość, którą daje nam Spring w postaci wildcardów określających położenie konfiguracji - w tym przypadku **classpath:/module/\*-context.xml**. Oznacza to, że Spring przeskanuje wszystkie biblioteki od których zależy projekt i dołączy ich konfigurację do głownej, dzięki czemu będziemy mogli uzyskać bardziej modułową i elastyczną budowę aplikacji. Może [Dynamic Modules](http://www.springframework.org/osgi) to nie jest ale na codzień w zupełności wystarcza :).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app id="ocs" version="2.5"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    xsi:schemaLocation="
        http://java.sun.com/xml/ns/javaee
        http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    >
 
    <!-- Informacje nazewnicze dla kontenera -->
    <display-name>Maven Search Services :: Webapp</display-name>
    <description>Frontend indeksera.</description>
 
    <!-- Lokalizacje plików konfiguracyjnych -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>
            <!-- Konfiguracja zabezpieczeń opartych o spring secuirty -->
            /WEB-INF/security.xml
            <!-- Konfiguracja CXF -->
            /WEB-INF/cxf-beans.xml
            <!-- Konfiguracja kontekstu -->
            /WEB-INF/applicationContext.xml
            <!-- Wczytanie konfiguracji modułów -->
            classpath:/module/*-context.xml
        </param-value>
    </context-param>
 
    <!-- Servlet obsługujący usługi sieciowe -->
    <servlet>
        <servlet-name>CXFServlet</servlet-name>
        <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
 
    <!-- Mapowanie adresu usług sieciowych -->
    <servlet-mapping>
        <servlet-name>CXFServlet</servlet-name>
        <url-pattern>/services/*</url-pattern>
    </servlet-mapping>
 
    <!-- Inicjowanie kontekstu springa -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
 
</web-app>
```

### cxf-beans.xml

Plik ten zawiera definicje konfiguracyjne CXFa. W poniższej formie włączana jest tylko część modułów CXF w celu zmniejszenia użycia zasobów.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:cxf="http://cxf.apache.org/core"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://cxf.apache.org/core
        http://cxf.apache.org/schemas/core.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
    ">
 
    <!-- Moduły CXF-a -->
    <import resource="classpath:META-INF/cxf/cxf.xml"/>
    <import resource="classpath:META-INF/cxf/cxf-extension-soap.xml"/>
    <import resource="classpath:META-INF/cxf/cxf-extension-http-binding.xml"/>
    <import resource="classpath:META-INF/cxf/cxf-servlet.xml"/>
 
</beans>
```

Pozostałe pliki konfiguracyjne są praktycznie puste, ponieważ tyczą się obszarów niezwiązanych z tematem tego wpisu. Po tym wszystkim możemy uruchomić naszą aplikację poleceniem **mvn jetty:run**. Po wejściu na adres http://localhost:8080/webapp/services naszym oczom powinna ukazać się lista podobna do poniższej:
![services](/wp-content/uploads/2008/07/services.jpg)
Nie wiem niestety dlaczego CXF generuje zły adres usługi (pomijający mapowanie servletu) oraz ma problemy z wystawieniem WSDLa. Jakkolwiek i bez tego wszystko działa poprawnie - znaczy wywoływanie usług rzecz jasna. :)

![soap-ui](/wp-content/uploads/2008/07/soap-ui-300x182.jpg)](/wp-content/uploads/2008/07/soap-ui.jpg) W tym momencie możemy uruchomić [Soap UI](http://www.soapui.org/) i wykonać jedyną dostępną metodę - findArtifact.

## Client

Ostatnim z elementów, który pozostał do omówienia jest klient. Niestety z racji na to, że ta nota już się wystarczająco rozrosła zrobię to w kolejnym wpisie. Mam nadzieję, że nota ta nieco ułatwi przyszłym użytkownikom CXFa jego poznanie. :) Dla tych, którym nie chce się wpisywać tego wszystkiego w edytorze [zamieszczam źródła](http://media.dywicki.pl/blog/cxf/cxf.zip) razem z działającym klientem. Projekty nie są nadzwyczajnie dopieszczone, ale w zupełności wystarczą do startu. Znajduje się też tam w pełni działający klient stworzonej usługi. Pozdrawiam i życzę miłej zabawy!

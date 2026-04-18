---
author: splatch
category:
  - java
  - porady
  - spring
date: "2008-11-20T06:00:46+00:00"
guid: http://blog.dywicki.pl/?p=233
summary: _Jako, że nie zawsze mam czas pisać dłuższe noty, a nie wszyscy znają Springa postanowiłem publikować krótkie porady, które mogą kiedyś komuś się przydać._
title: Odwołanie do klasy Properties w kontekście springa
url: /2008/11/20/odwolanie-do-klasy-properties-w-kontekscie-springa/

---
_Jako, że nie zawsze mam czas pisać dłuższe noty, a nie wszyscy znają Springa postanowiłem publikować krótkie porady, które mogą kiedyś komuś się przydać._

Klasa java.util.Properties to jeden z najprostszych sposobów na odwołanie do prostych ustawień konfiguracyjnych. Składnia pliku properties jest zbliżona do ini i wygląda następująco:
```properties
nazwa.wlasnosci wartosc
inna.nazwa.wlasnosci=costam
\# to jest komentarz
jeszcze.cos.nowego
```

Jeśli chcemy przekazać obiekt klasy Properties do jakiegoś innego obiektu możemy skorzystać z poniższej wstawki w kontekście springa:
```xml
<bean class="org.springframework.beans.factory.config.PropertiesFactoryBean">
    <property name="location" value="classpath:corba.properties" />
</bean>
```

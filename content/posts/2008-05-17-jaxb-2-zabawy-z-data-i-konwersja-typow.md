---
author: splatch
category:
  - java
  - jaxb
date: "2008-05-17T09:10:27+00:00"
guid: http://blog.dywicki.pl/2008/05/17/jaxb-2-zabawy-z-data-i-konwersja-typow/
summary: Jedną z bolączek JAXB jest problematyczna obsługa dat i czasów. Przypomnijmy sobie schemat użyty [w jednej z wcześniejszych not](http://blog.dywicki.pl/?p=190).
title: JAXB 2, zabawy z datą i konwersją typów
url: /2008/05/17/jaxb-2-zabawy-z-data-i-konwersja-typow/

---
Jedną z bolączek JAXB jest problematyczna obsługa dat i czasów. Przypomnijmy sobie schemat użyty [w jednej z wcześniejszych not](/?p=190).

```xml
 <!-- Definicja książki -->
 <complextype name="Book">
 <sequence>
 <element name="title" type="string" minOccurs="1" />
 <element name="isbn" type="string" />
 <element name="releaseDate" type="date" />
 <element name="author" type="tns:Author" maxOccurs="unbounded" />
 </sequence>
 </complextype>
```

Jak widać, każda książka ma określoną datę wydania. Jakkolwiek typ date z XML Schema nie jest bezpośrednio odwzorowany do java.util.Date czy też java.util.Calendar. W przypadku implementacji dostarczonej przez Sun mamy do czynienia z obiektami klasy [XMLGregorianCalendarImpl](http://www.javaresearch.org/source/jdk150/com/sun/org/apache/xerces/internal/jaxp/datatype/XMLGregorianCalendarImpl.java.html). Rozwiązaniem problemu jest dodanie odpowiedniego adaptera. Możemy robić to ręcznie ale najwygodniejszym rozwiązaniem będzie skorzystanie z gotowca - klasy [javax.xml.bind.DatatypeConverter](http://java.sun.com/webservices/docs/1.6/api/javax/xml/bind/DatatypeConverter.html). Jej użycie wygląda następująco:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<schema xmlns="http://www.w3.org/2001/XMLSchema" targetNamespace="http://dywicki.pl/court"
 xmlns:jaxb="http://java.sun.com/xml/ns/jaxb" jaxb:version="2.0"
 xmlns:tns="http://dywicki.pl/court" elementFormDefault="qualified">
 <annotation>
 <appinfo>
 <jaxb:globalBindings>
 <jaxb:javaType name="java.util.Calendar" xmlType="date"
 parseMethod="javax.xml.bind.DatatypeConverter.parseDate"
 printMethod="javax.xml.bind.DatatypeConverter.printDate" />
 </jaxb:globalBindings>
 </appinfo>
 </annotation>

 <!-- tutaj deklaracje typów -->
</schema>
```

Podobne rozwiązanie można wykorzystać na poziomie pojedyńczego elementu aby zmapować konkretny element do obiektu. Element annotation można również dodać do deklaracji simpleType (powiedzmy reprezentacja numeru pesel + proste restrykcje).
```xml
 <complextype name="File">
 <sequence>
 <element name="category" type="string">
 <annotation>
 <appinfo>
 <jaxb:javaType
 name="pl.dywicki.court.Category"
 parseMethod="pl.dywicki.court.Category.parse"
 printMethod="pl.dywicki.court.Category.print"
 />
 </appinfo>
 </annotation>
 </element>
 </sequence>
 </complextype>
```

JAXB wygeneruje wówczas odpowiednie adnotacje dla tworzonych klas oraz adaptery, które odwołują się do metod wskazanych w atrybutach elementu **javaType**.

W moim przypadku kod klasy Category wygląda następująco:
```java
package pl.dywicki.court;

/**
 * Reprezentacja kategorii sprawy.
 *
 * @author Lukasz Dywicki <a href="mailto:luke@code-house.net">luke@code-house.net</a>
 **/
public class Category {

 private String name;

 public Category(String value) {
 this.name = value;
 }

 public static Category parse(String value) {
 return new Category(value);
 }

 public static String print(Category value) {
 return value.name;
 }
}
```

Jest to oczywiście maksymalnie uproszczony przykład. W konstruktorze bądź metodach, które konwertują string do obiektu możemy podpiąć walidację i bronić się wyjątkami przed nieprawidłowymi wartościami by uniemożliwić deserializację dokumentu.

---
author: splatch
category:
  - eclipse
  - java
  - jaxb
  - tlumaczenia
  - xml
date: "2007-10-14T17:29:57+00:00"
guid: http://blog.dywicki.pl/?p=190
summary: W ramach [WarsJava](http://groups.google.com/group/warszawa-jug/web/warsjava), konferencji/warsztatów organizowanych przez [Warszawski JUG](http://warszawa.jug.pl/), będę miał szansę przedstawić publiczności [Java Architecture for XML Binding](https://jaxb.dev.java.net/) w wersji drugiej. Swoje boje z JAXB postanowiłem opisać na blogu, być może dla kogoś temat wyda się interesujący.. :)
title: JAXB 2, Wprowadzenie
url: /2007/10/14/jaxb-2-wprowadzenie/

---
W ramach [WarsJava](http://groups.google.com/group/warszawa-jug/web/warsjava), konferencji/warsztatów organizowanych przez [Warszawski JUG](http://warszawa.jug.pl/), będę miał szansę przedstawić publiczności [Java Architecture for XML Binding](https://jaxb.dev.java.net/) w wersji drugiej. Swoje boje z JAXB postanowiłem opisać na blogu, być może dla kogoś temat wyda się interesujący.. :)

## Ogólniki

JAXB ma za zadanie ułatwić pracę z XML poprzez automatyczne dostarczanie obiektów zamiast żmudnego, ręcznego obrabiania plików SAX, DOM czy StAX. Jest to dodatkowa warstwa zbudowana na bazie [Java Api for XML Processing](http://java.sun.com/webservices/jaxp/index.jsp). Druga wersja JAXB jest dołączona do J2SE 1.6.

Spójrzmy teraz na schemat budowy JAXB:
![JAXB, schemat](/wp-content/uploads/2007/10/jaxb-overview.gif)

1. Kompilator
   1. **Schema** \- dokument opisujący strukturę dokumentu - może to być XML Schema, DTD jak również Relax NG czy też WSDL.

   1. **XML /Java Binding Customization** \- JAXB daje nam możliwość wpływania na wygenerowany kod poprzez ten właśnie mechanizm, dzięki temu można określić np. metody, które będą tworzyć elementy w przypadku odczytu z dokumentu bądź serializować w przypadku zapisu.
1. Runtime
   1. **Portable JAXB-annotated classes** \- kod, który wygenerowaliśmy przy użyciu kompilatora bądź klasy, do których dodaliśmy adnotacje.
   1. **Object Factory** \- klasa, która tworzy obiektowe reprezentacje naszych elementów XML.
   1. **Binding runtime framework implementation** \- obsługuje proces odczytu danych ( _unmarshalling_) oraz ich zapisu ( _marshalling_), obsługuje również walidację. Element ten do poprawnego działania wymaga elementów wymienionych powyżej - czyli klas z adnotacjami oraz ich "fabryki".

## Pierwszy projekt

Pamiętam, że kiedyś chciałem stworzyć prostą aplikację w której mógłbym notować jakie książki mam i komu je wypożyczyłem. Niestety w którymś momencie zabrakło chyba sił i poza definicją struktury dokumentu stworzyłem tyko kilka linii kodu. Nie mniej, po drobnych przeróbkach udało się doprowadzić schemat do porządku. W aplikacji będą występować następujące instancje klas:

1. **Books** \- repozytorium książek, zawiera listę książek
1. **Book** \- definicja książki - atrybuty to title, isbn oraz lista autorów
1. **Author** \- reprezentacja autora - atrybuty firstName, lastName. Dodatkowo opcjonalny jest type wskazujący czy mamy do czynienia z tłumaczem czy też z "normalnym" autorem.
1. **AuthorType** \- typ wyliczeniowy - dostępne wartości to Translator, Author

Aby korzystało się nam z kompilatora (xjc) wygodniej podepniemy go pod Eclipse jako narzędzie zewnętrzne. Tu przyda się kilka informacji na temat samego kompilatora:
```sh
xjc [-options ...] file/URL/dir ... [-b bindinfo] ...
```
Gdzie najważniejsze opcje to:

1. **-d**, katalog do którego trafi wygenerowany kod
1. **-b**, dodatkowe mappingi umożliwiające zmianę zachowania kompilatora
1. **-p**, paczka do której trafi wygenerowany kod
1. **-classpath**, miejsca w których xjc ma szukać klas, które zostały użyte w mappingach

Przykładowo
```bash
xjc -d generated schema.xsd
```

[![Konfiruracja external tools](/wp-content/uploads/2007/10/jaxb-external.thumbnail.JPG)](/wp-content/uploads/2007/10/jaxb-external.JPG "Konfiruracja external tools")
Spowoduje, że klasy wylądują w katalogu generated. Nazwa paczki zostanie zaczerpnięta z atrybutu targetNamespace dokumentu zawartego w schemacie. Obok znajduje się screen z konfiguracją - z ważniejszych informacji - **Working directory ustawione na ${project\_loc}** pozwala nam się nawigować po katalogach względem aktualnie zaznaczonego w nawigatorze projektu. **wartość w polu Arguments -d generated ${resource\_loc} -verbose -extension -npa** oznacza, że wygenerowane klasy wylądują w katalogu "generated" (należy stworzyć taki source folder). Zmienna ${resource\_loc} pozwala nam użyć aktualnie zaznaczonego pliku w nawigatorze. Dzięki takiej konfiguracji niezależnie od projektu i pliku jesteśmy w stanie wygenerować potrzebne nam klasy poprzez dwa kliknięcia - pierwsze w nawigatorze, drugie na ikonie external tools.

Schemat jest [dostępny do ściągnięcia](http://media.dywicki.pl/blog/jaxb/books.xsd), podobnie jak [przykładowy plik z danymi](http://media.dywicki.pl/blog/jaxb/books.xml). Proszę zwrócić uwagę na to jak jest zdefiniowany główny element dokumentu - poprzez zawarcie typu anonimowego. Jeśli zdefiniujemy oddzielnie element i typ to wówczas konieczne będzie ręczne dodanie adnotacji @XmlRootElement przy klasie Books. O tym [dlaczego JAXB się tak zachowuje](http://weblogs.java.net/blog/kohsuke/archive/2006/03/why_does_jaxb_p.html) można wyczytać na jednym z Sun-owskich blogów.

```java
package pl.dywicki.books.app;

import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;

import pl.dywicki.books.Author;
import pl.dywicki.books.AuthorType;
import pl.dywicki.books.Book;
import pl.dywicki.books.Books;
import pl.dywicki.books.ObjectFactory;

/**
 * Testowa klasa pokazująca użycie JAXB do zapisywania danych.
 *
 * @author Łukasz Dywicki
 */
public class App {

 public static void main(String[] args) throws Exception {

 // repozytorium książek
 Books books = new Books();

 // przykładowa książka
 Book book = new Book();
 book.setTitle("Test title");
 book.setIsbn("111-111-111");

 // testowy autor
 Author author = new Author();
 author.setFirstName("Martin");
 author.setLastName("Fowler");
 book.getAuthor().add(author);

 // testowy tłumacz
 author = new Author();
 author.setFirstName("Łukasz");
 author.setLastName("Dywicki");
 author.setType(AuthorType.TRANSLATOR);
 book.getAuthor().add(author);

 // dodanie książki do repozytorium
 books.getBook().add(book);

 JAXBContext context = JAXBContext.newInstance(ObjectFactory.class);
 Marshaller marshaller = context.createMarshaller();
 // chcemy ładnych wcięć w wyniku! :)
 marshaller.setProperty("jaxb.formatted.output", true);
 marshaller.marshal(books, System.out);
 }

}
```

Po uruchomieniu tego przykładu w konsoli powinien pokazać się taki oto wynik:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ns2:books xmlns:ns2="http://dywicki.pl/books">
 <book>
 <title>Test title</title>
 <isbn>111-111-111</isbn>
 <author>
 <firstname>Martin</firstname>
 <lastname>Fowler</lastname>
 </author>
 <author>
 <firstname>Łukasz</firstname>
 <lastname>Dywicki</lastname>
 <type>Translator</type>
 </author>
 </book>
</ns2:books>
```
(Zamiast ns2\_books powinno być ns2:books, ale narzędzie kolorujące składnie sobie nie radzi).

Dla zainteresowanych załączam [źródła projektu](http://media.dywicki.pl/blog/jaxb/jaxb-test.zip).

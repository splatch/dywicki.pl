---
author: splatch
category:
  - uncategorized
date: "2011-06-14T14:22:54+00:00"
guid: http://blog.dywicki.pl/?p=230
title: XML Schema Design Patterns
url: /2011/06/xml-schema-design-patterns/

---
XML Schema jest bodajże najlepszym sposobem walidacji dokumentów XML. Model zastosowany w przypadku tego meta-języka pozwala na tworzenie złożonych konstrukcji. W oparciu o niego można budować własne rozszerzenia czy też zagnieżdżać w sekcjach xsd:appinfo dodatkowe metadane. Dzisiaj jednak nie o tym, a o wzorcach projektowych. Sam się zdziwiłem gdy trafiłem na artykuł [Introducing Design Patterns in XML Schemas](http://developers.sun.com/jsenterprise/archive/nb_enterprise_pack/reference/techart/design_patterns.html). W życiu się nie zastanawiałem czy to co piszę w XSD ma coś wspólnego z wzorcami czy nie. Sun wyprzedził w tym momencie chyba wszystkich. :-)

Wzorce, które zostały wymienione we wspomnianym artykule odnoszą się do powiązania definiowanych typów z definiowanymi elementami. Ciężko mi się zgodzić z tym, że wybór wzorca jest krytyczny przy projektowaniu schematu, ponieważ schemat zazwyczaj ma przeznaczenie już w chwili pisania i zazwyczaj nie możemy powiedzieć, zrobimy to wzorcem X, ponieważ sam nasuwa się wzorzec Y. Ale to tak tylko moim zdaniem.

Tabelka poniżej prezentuje zawartość przeniesioną ze strony Suna. Zawiera ona 4 najpopularniejsze wzorce. Dwa najczęściej spotykane w internecie to Venetian Blind oraz Garden of Eden ze względu na to, że są bardzo podatne na ponowne użycie.
WzorzecCharakterystyka**Russian Doll**, [przykład](#Russian_Doll_Sample)Zawiera jeden element globalny, pozostałe są lokalne.

- Jest tylko jeden poprawny element.
- Może uprościć przestrzeń nazw poprzez zastosowanie atrybutu elementFormDefault dla elementu xsd:schema.

- Nadaje się tylko dla pojedynczych schematów.
- Pozwala na ponowne użycie tylko całej gałęzi, a nie każdego typu z osobna.

**Salami Slice**, [przykład](#Salami_Slice_Sample)Wszystkie elementy są globalne, stąd każdy może być użyty w charakterze root node'a.

- Wszystkie elementy można ponownie użyć.
- Łatwe wiązanie schematów pomiędzy plikami.

- Powoduje większą złożoność w przestrzeni nazw.
- Trudny do określenia root.

**Venetian Blind**, [przykład](#Venetian_Blind_Sample)Pochodna Russian Doll, zawiera jeden element globalny, pozostałe są lokalne

- Zawiera tylko jeden element nadrzędny.
- Pozwala na ponowne użycie wszystkich typów oraz elementu nadrzędnego.
- Łatwa praca z wieloma plikami.

- Ograniczona enkapsulacja poprzez ekspozycję wszystkich typów.

**Garden of Eden**, [przykład](#Garden_of_Eden_Sample)Połączenie Venetian Blind oraz Salami Slice. Wiele elementów globalnych, wiele typów publicznych. Wiele kandydatów na root node.

- Pozwala ponownie użyć elementy oraz typy.
- Łatwa praca z wieloma plikami.

- Zawiera wiele potencjalnych elementów nadrzędnych.
- Ograniczona enkapsulacja.
- Trudna do czytania i zrozumienia.

_Źródło: Sun Developers Network_

## Przykłady

### Russian Doll

```xml
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
 targetNamespace="http://schemas.sun.com/point/russiandoll"
 xmlns:tns="http://schemas.sun.com/point/russiandoll"
 elementFormDefault="qualified">

 <xsd:element name="Line">
 <xsd:complexType>
 <xsd:sequence>
 <xsd:element name="PointA">
 <xsd:complexType>
 <xsd:attribute name="x" type="xsd:integer"/>
 <xsd:attribute name="y" type="xsd:integer"/>
 </xsd:complexType>
 </xsd:element>
 <xsd:element name="PointB">
 <xsd:complexType>
 <xsd:attribute name="x" type="xsd:integer"/>
 <xsd:attribute name="y" type="xsd:integer"/>
 </xsd:complexType>
 </xsd:element>
 </xsd:sequence>
 </xsd:complexType>
 </xsd:element>
</xsd:schema>
```

### Salami Slice

```xml
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
 targetNamespace="http://schemas.sun.com/point/salami"
 xmlns:tns="http://schemas.sun.com/point/salami"
 xmlns="http://schemas.sun.com/point/salami"
 elementFormDefault="qualified">

 <xsd:element name="PointA">
 <xsd:complexType>
 <xsd:attribute name="x" type="xsd:integer"/>
 <xsd:attribute name="y" type="xsd:integer"/>
 </xsd:complexType>
 </xsd:element>

 <xsd:element name="PointB">
 <xsd:complexType>
 <xsd:attribute name="x" type="xsd:integer"/>
 <xsd:attribute name="y" type="xsd:integer"/>
 </xsd:complexType>
 </xsd:element>

 <xsd:element name="Line">
 <xsd:complexType>
 <xsd:sequence>
 <xsd:element ref="PointA"/>
 <xsd:element ref="PointB"/>
 </xsd:sequence>
 </xsd:complexType>
 </xsd:element>
</xsd:schema>
```

### Venetian Blind

```xml
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
 targetNamespace="http://schemas.sun.com/point/venetianblind"
 xmlns:tns="http://schemas.sun.com/point/venetianblind"
 xmlns="http://schemas.sun.com/point/venetianblind"
 elementFormDefault="qualified">

 <xsd:complexType name="PointType">
 <xsd:attribute name="x" type="xsd:integer"/>
 <xsd:attribute name="y" type="xsd:integer"/>
 </xsd:complexType>

 <xsd:element name="Line">
 <xsd:complexType>
 <xsd:sequence>
 <xsd:element name="PointA" type="PointType"/>
 <xsd:element name="PointB" type="PointType"/>
 </xsd:sequence>
 </xsd:complexType>
 </xsd:element>
</xsd:schema>
```

### Garden of Eden

```xml
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"
 targetNamespace="http://schemas.sun.com/point/gardenofeden"
 xmlns="http://schemas.sun.com/point/gardenofeden"
 elementFormDefault="qualified">

 <xsd:complexType name="PointType">
 <xsd:attribute name="x" type="xsd:integer"/>
 <xsd:attribute name="y" type="xsd:integer"/>
 </xsd:complexType>

 <xsd:complexType name="LineType">
 <xsd:sequence>
 <xsd:element ref="PointA"/>
 <xsd:element ref="PointB"/>
 </xsd:sequence>
 </xsd:complexType>

 <xsd:element name="PointA" type="PointType"/>

 <xsd:element name="PointB" type="PointType"/>

 <xsd:element name="Line" type="LineType"/>
</xsd:schema>
```

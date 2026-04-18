---
author: splatch
category:
  - db
  - java
  - wiadomości
date: "2007-11-14T23:07:48+00:00"
guid: http://blog.dywicki.pl/?p=198
summary: Wczoraj do mojej skrzynki trafił newsletter z db4o, w którym trafiłem na dwie informacje. Pierwsza głosi, że db4o w wersji 6.4 jest gotowe do zastosowań produkcyjnych. Druga, że dostępna jest beta wersji 7.0 z nowym mechanizmem o nazwie 'transparent activation'. Postanowiłem [zajrzeć do szczegółów](http://developer.db4o.com/blogs/product_news/archive/2007/11/12/the-7th-dimension.aspx), które tyczą się najnowszej wersji. Wynikiem czego jest to dosyć luźne tłumaczenie.
title: Nowa wersje db4o
url: /2007/11/15/nowa-wersje-db4o/

---
Wczoraj do mojej skrzynki trafił newsletter z db4o, w którym trafiłem na dwie informacje. Pierwsza głosi, że db4o w wersji 6.4 jest gotowe do zastosowań produkcyjnych. Druga, że dostępna jest beta wersji 7.0 z nowym mechanizmem o nazwie 'transparent activation'. Postanowiłem [zajrzeć do szczegółów](http://developer.db4o.com/blogs/product_news/archive/2007/11/12/the-7th-dimension.aspx), które tyczą się najnowszej wersji. Wynikiem czego jest to dosyć luźne tłumaczenie.

- **Aktywacja**  

  Wyobraź sobie drzewo obiektów, które są ładowane z db4o. Kiedy Twoja aplikacja dobiera się do własności z korzenia z drzewa, normalnym zdaje się wczytanie całego drzewa do pamięci, prawda? Z tym, że jeśli drzewo jest rozłożyste, zajmuje to wiele czasu i wile pamięci. db4o dostarcza teraz funkcjonalności, która wczytuje tylko te obiekty, których na prawdę potrzebujesz. Proces ładowania tych własności nazwaliśmy właśnie 'aktywacją'.

- **Zapytania natywne**  

  Zapytania natywne są w pełni bezpieczne (typesafe?), można je refaktoryzować. Są również normalnie obsługiwane w czasie kompilacji, zgodnie ze składnią języka, w którym został napisany program. Zaglądając pod maskę - wytażenie zapisane przy użyciu konstrukcji języka jest analizowane i konwertowane tak by używać indeksów tam gdzie tylko można. Dla zainteresowanych [PDF w języku angielskim](http://www.db4o.com/about/productinformation/whitepapers/Native%20Queries%20Whitepaper.pdf).

- **Wydajność**  

  Obydwa powyższe mechanizmy są przeźroczyste, jakkolwiek Twój byte-code po kompilacji musi zostać przeanalizowany i zoptymalizowany by wykorzystać zaawansowane możliwości, które oferuje db4o. Optymalizator jest dostępny jako task dla Ant bądź jako aplikacja napisana w Javie. Możesz wykonywać swoją pracę w sposób, w jaki robiłeś to do tej pory, a Twoje aplikacje oparte o db4o będą po prostu działały szybciej i zabierały mniej pamięci.

Jak twierdzą Twórcy db4o - z wydaniem wersji developerskiej 7.0, dzięki połączeniu 3 wymienionych wyżej technik, zrobili duży krok w przód, w kierunku realizacji zasadniczych celów, dla których powstaje ich produkt:

- Developer nie powinien pisać kodu wiążącego się z zapisywaniem obiektów. To jest rola, którą ma pełnić db4o.
- Standardowy język programowania. Idealna obiektowa baza danych używa konstrukcji dostarczanych w języku programowania oraz nie narzuca nowej składni.
- Łatwość użycia nie ponosi za sobą straty wydajności. Kod, którym zarządza system utrwalania obiektów może być wydajniejszy od pisanego z ręki oszczędzając równocześnie więcej pamięci.

Kontynuując dalej czytanie newsa trafiamy na pytanie - "Wszystko do brzmi jak typowy techniczny bełkot, ale jak to wszystko może mi pomóc w realizacji projektów w ramach czasowych i w jaki sposób przekłada się na szczęście mojego klienta?"
W odpowiedzi wymienione zostają plusy, jakie niosą ze sobą nowe możliwości z perspektywy użytkownika:

- Możesz zachować swój spójność i porządek swojego kodu nawet gdy w grę wchodzi odczytywanie obiektów z bazy
- Używając db4o z dużymi drzewami obiektów staje się łatwiejsze, ponieważ nie musisz już dłużej przejmować się inicjowaniem obiektów w Twoim kodzie
- Twoja aplikacja będzie działała szybciej równocześnie pochłaniając mniej zasobów, ponieważ wczyta obiekty których potrzebuje w danej chwili.
- Nowy optymalizator może być uruchamiany z poziomu Twojego IDE, optymalizując klasy i jar-y w locie. Nie będziesz musiał zmieniać zachowania swojego IDE w żaden sposób by móc skorzystać z mechanizmu aktywacji i optymalizacji zapytań.

Dlaczego o tym wszystkim wspominam? :) Ponieważ zastanawiam się czy użyć db4o do obsługi "modelu" w Agavi IDE. Modelem w przypadku Agavi IDE były by przede wszystkim informacje o modułach, akcjach itp.

---
author: splatch
category:
  - agavi
  - framework
  - php
  - wiadomości
date: "2007-06-18T21:41:11+00:00"
guid: http://blog.dywicki.pl/?p=146
summary: 'Dzisiaj rano światło dzienne ukazało się Agavi 0.11 RC5, oprócz poprawek błędów z wersji RC4 doszło parę nowości:'
title: Agavi 0.11 RC5
url: /2007/06/18/agavi-011-rc5/

---
Dzisiaj rano światło dzienne ukazało się Agavi 0.11 RC5, oprócz poprawek błędów z wersji RC4 doszło parę nowości:
\- Pełne wsparcie dla generowania WSDL, automatyczne mapowanie akcji a nawet wsparcie dla obsługi nagłówków SOAP.
\- Wsparcie dla transformacji plików konfiguracyjnych poprzez zamieszczanie instrukcji < ?xml-stylesheet?>
\- Wsparcie dla przestrzeni nazw w plikach konfiguracyjnych
\- Automatyczna obsługa magic\_quotes dla zmiennych superglobalnych

Pojawiły się drobne zmiany w API do obsługi configów oraz nowy sposób ich odczytu, który wejdzie do użytku w wersji 1.0. Póki co jest tylko testowy a jedynym handlerem, który został zaimplementowany "na nowo" jest ten obsługujący WSDL.

Dodam, że [Agavi](http://agavi.org) to pierwszy framework PHP, który rozwiązuje kwestie dostępu do akcji w tak elastyczny sposób. Niezależnie od tego jakim sposobem zostało przysłane żądanie akcja wygląda tak samo. Jest to krok w stronę dojrzałych rozwiązań tj [Spring Framework](http://springframework.org). Pytanie tylko - czy developerom uda się zachować pierwotną prostotę i ile pracy trzeba będzie włożyć w użycie danego rozwiązania? Odpowiedź na nie pozostaje póki co zagadką a pełnej oceny będziemy mogli dokonać po ukazaniu się wersji 1.0 wraz z dokumentacją.

Zainteresowanych naturalnie Agavi zapraszam do [traca](http://trac.agavi.org/) i przeglądania [źródeł wersji 0.11 RC5](http://trac.agavi.org/browser/tags/0.11.0RC5/). :)

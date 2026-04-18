---
author: splatch
category:
  - framework
  - java
  - log4j
date: "2008-10-15T08:00:10+00:00"
guid: http://blog.dywicki.pl/?p=229
summary: '[Log4j](http://logging.apache.org/log4j/) jest najpopularniejszą biblioteką do logowania dla Javy. Została ona wydana już jakiś czas temu i w chwili obecnej rozwija się znacznie wolniej niż kiedyś, warto jednak nadmienić że społeczność wciąż dostarcza nowych możliwości. Może najpierw o samej strukturze log4j.'
title: Log4j, przejrzyste komunikaty
url: /2008/10/15/log4j-przejrzyste-komunikaty/

---
[Log4j](http://logging.apache.org/log4j/) jest najpopularniejszą biblioteką do logowania dla Javy. Została ona wydana już jakiś czas temu i w chwili obecnej rozwija się znacznie wolniej niż kiedyś, warto jednak nadmienić że społeczność wciąż dostarcza nowych możliwości. Może najpierw o samej strukturze log4j.

## Najważniejsze elementy

Log4j ma kilka składowych:

- Appender
- Layout
- Category

Dzisiaj skupię się tylko na dwóch pierwszych. Appender jest odpowiedzialny za zapis bądź wysyłanie komunikatów do wybranego miejsca podczas gdy Layout konwertuje wpis do określonego formatu.

Z najciekawszych Appenderów wymienię

- [RollingFileAppender](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/RollingFileAppender.html) rotuje logi do określonego rozmiaru, po osiągnięciu np 10mb kopiuje stary plik zgodnie ze schematem i zaczyna zapisywać do nowego.
- [DailyRollingFileAppender](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/DailyRollingFileAppender.html) zachowuje się podobnie jak wyżej wymieniony z tym, że umożliwia zapis z podziałem na lata, miesiące, dni czy nawet minuty.
- [SyslogAppender](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/net/SyslogAppender.html), który wysyła komunikaty do [Sysloga](http://pl.wikipedia.org/wiki/Syslog)
- [LF5Appender](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/lf5/LF5Appender.html) wyświetlający logi w okienku tworzonym przy pomocy Swinga.

- [SMTPAppender](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/net/SMTPAppender.html) wysyłający maile z komunikatami. Jeśli się go źle skonfiguruje gwarantowane zapchanie skrzynki ;-)
- [NagiosAppender](http://sourceforge.net/projects/nagiosappender/) przesyłający komunikaty do [Nagiosa](http://www.nagios.org/), narzędzia służącego do monitorowania.

Mając już appender, który wie gdzie zapisywać wypada powiedzieć Log4j też **co powinien zapisywać**.

- [HTMLLayout](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/HTMLLayout.html) produkujący tabelę HTML
- [PatternLayout](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/PatternLayout.html) posiadający możliwość określenia "szablonu" wpisu.

Więcej Layoutów można znaleźć w [Javadocu Log4j](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/Layout.html).

Jako, że zdecydowanie najczęściej używa się PatternLayout dodam parę słów na temat tego co potrafi. We wzorze który będzie później użyty do stworzenia komunikatu możemy używać następujących wyrażeń:

- **%c** kategoria zdarzenia
- **%C** nazwa klasy z której zostało wysłane zdarzenie
- **%d** data zdarzenia (przykład: %d{HH:mm:ss,SSS} )
- **%m** komunikat wysłany z aplikacji
- **%n** separator lini
- **%p** priorytet zdarzenia
- **%r** ilość milisekund, które zdążyły upłynąć od startu aplikacji
- **%t** nazwa wątku z którego zostało wysłane zdarzenie
- **%x** kontekst diagnostyczny powiązany z wątkiem (wie ktokolwiek co to znaczy?)
- **%%** pojedynczy znak %
- **%M** nazwa metody z której zostało wysłane zdarzenie (uwaga, bardzo wolne)
- **%L** numer linii z której zdarzenie pochodzi (uwaga, bardzo wolne)
- **%F** nazwa pliku z którego pochodzi zdarzenie (uwaga, bardzo wolne)
- **%l** informacja o wywołującym zdarzenie (uwaga, bardzo wolne, wskazuje na lokalizację z naszego kodu)

Przykłady modyfikatorów:

- **%20c** dodaje spacje z lewej strony jeśli nazwa kategorii jest krótsza niż 20 znaków
- **%-20c** dodaje spacje z prawej strony jeśli nazwa kategorii jest krótsza niż 20 znaków
- **%.30c** przycina kategorię pozostawiając ostatnie 30 znaków
- **%20.30c** wypełnia z lewej strony nazwę kategorię spacjami do 20 znaków oraz przycina początkowe znaki jeżeli jest dłuższa on 30 znaków
- **%-20.30c** analogicznie jak wyżej, z tym że znaki zostaną dodane z prawej strony. Ze swojej strony polecam używanie formatu np %-20.20, co zagwarantuje że element będzie miał zawsze 20 znaków. Jeśli pozostawimy **-20.30** to element będzie miał 20 znaków jeśli kategoria jest zbyt krótka a 30 gdy będzie za długa

## Przykład konfiguracji

Log4j można konfigurować na kilka sposobów. Programowo, przez plik properties oraz konfigurację XML. Zdecydowanie najpopularniejszy jest plik properties. Jego użycie jest banalnie proste. Włączamy log4j.jar do tak by był on dostępny w classpathu, a następnie tworzymy plik _log4j.properties_, który domyślnie jest poszukiwany przez bibliotekę jeśli nie została przekazana żadna konfiguracja. Aby jednak to się stało nasz kod musi spróbować wysłać jakąś wiadomość do Log4j.

```properties
log4j.rootLogger DEBUG, out

# loggery
log4j.logger.org.springframework INFO
log4j.logger.org.apache.activemq INFO
log4j.logger.org.apache.activemq.spring WARN
log4j.logger.org.apache.servicemix DEBUG, stdout

#appendery
log4j.appender.stdout org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern %d{ABSOLUTE} | %-5p | %t | %c | %m%n

log4j.appender.out org.apache.log4j.DailyRollingFileAppender
log4j.appender.out.DatePattern '-'yyyy-MM-dd'.log'"/>
log4j.appender.out.layout org.apache.log4j.PatternLayout
log4j.appender.out.layout.ConversionPattern %d{ABSOLUTE} | %-5p | %t | %c | %m%n
log4j.appender.out.file E:/fuse-esb/data/log/servicemix
```

Założenie powyższej konfiguracji jest następujące. Domyślny poziom logowania jest ustawiony na _DEBUG_ a komunikaty są zapisywane do pliku (rootLogger). Zdefiniowane są dwa appendery _stdout_ oraz _out_. Pierwszy jest użyty tylko dla paczki _org.apache.servicemix_. Pozostałe loggery mają określony tylko poziom logowania. Pliki z logami będą dzielone na dni, także w lokalizacji _E:/fuse-esb/data/log/_ po 3 dniach działania od 13 do 15 października będziemy mieli 3 pliki:

- servicemix - logi z bierzącego dnia
- servicemix-2008.10.14.log - logi z 14 października
- servicemix-2008.10.13.log - logi z 13 października

Mam nadzieję że od dzisiaj Log4j będzie Wam drodzy czytelnicy mówił więcej i prościej. Jeśli są jakieś kwestie które pozostają niewyjaśnione - proszę pytać.

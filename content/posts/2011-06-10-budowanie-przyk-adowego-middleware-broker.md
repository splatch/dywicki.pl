---
author: splatch
category:
  - middleware
date: "2011-06-10T16:45:13+00:00"
guid: http://dywicki.pl/?p=581
summary: |-
  Pod koniec maja świetnie spędziłem czas w Wielkiej Brytanii dostarczając consulting. Tuż po nim zacząłem myśleć o podzieleniu się ideą aplikacji middleware zbudowanej na ActiveMQ z funkcjami Camela. Spędziłem kilka dni tworząc wystarczający przykład.

  # Czym jest middleware?

  Middleware jest ogólnym terminem traktującym o oprogramowaniu które jest pewnego rodzaju pośrednikiem między systemami. Po cóż? - możesz zapytać. Generalnie ponieważ komunikacja pomiędzy punktami nie jest najlepsza do budowania większych aplikacji, co więcej niektóre komponenty pomiędzy komunikującymi pozwalają wstrzykiwać nową logikę bez zmieniania systemów komunikujących się. Czy możesz wyobrazić sobie sytuację gdzie wiele systemów tworzy komunikaty i wielu konsumentów tych komunikatów stworzonych w różnych językach? To jest typowy scenariusz gdzie middleware będzie użyteczne.
  Istnieje wiele narzędzi middleware i kilka ich kategorii, w tym wpisie dowiemy się jak używać ActiveMQ do budowania message oriented middleware. Co oznacza termin MOM? To, że mamy asynchroniczną komunikację bez bezpośrednich wywołań metod. Producent nic nie wie o konsumencie i na odwrót. Jeśli jesteś zainteresowany MOM-ami - zajrzyj do książki ["ActiveMQ in Action"](http://manning.com/snyder/) (napisanej przez commiterów ActiveMQ) z [wydawnictwa Manning](http://manning.com), w której termin ten jest opisany szerzej.
title: Budowanie przykładowego middleware - broker
url: /2011/06/budowanie-przykladowego-middleware-broker/

---
Pod koniec maja świetnie spędziłem czas w Wielkiej Brytanii dostarczając consulting. Tuż po nim zacząłem myśleć o podzieleniu się ideą aplikacji middleware zbudowanej na ActiveMQ z funkcjami Camela. Spędziłem kilka dni tworząc wystarczający przykład.

# Czym jest middleware?

Middleware jest ogólnym terminem traktującym o oprogramowaniu które jest pewnego rodzaju pośrednikiem między systemami. Po cóż? - możesz zapytać. Generalnie ponieważ komunikacja pomiędzy punktami nie jest najlepsza do budowania większych aplikacji, co więcej niektóre komponenty pomiędzy komunikującymi pozwalają wstrzykiwać nową logikę bez zmieniania systemów komunikujących się. Czy możesz wyobrazić sobie sytuację gdzie wiele systemów tworzy komunikaty i wielu konsumentów tych komunikatów stworzonych w różnych językach? To jest typowy scenariusz gdzie middleware będzie użyteczne.
Istnieje wiele narzędzi middleware i kilka ich kategorii, w tym wpisie dowiemy się jak używać ActiveMQ do budowania message oriented middleware. Co oznacza termin MOM? To, że mamy asynchroniczną komunikację bez bezpośrednich wywołań metod. Producent nic nie wie o konsumencie i na odwrót. Jeśli jesteś zainteresowany MOM-ami - zajrzyj do książki ["ActiveMQ in Action"](http://manning.com/snyder/) (napisanej przez commiterów ActiveMQ) z [wydawnictwa Manning](http://manning.com), w której termin ten jest opisany szerzej.

## Scenariusz

W naszym scenariuszu będziemy mieli kilka kolejek, każdy klient będzie posiadał własną kolejkę i konsumował komunikaty tylko z niej. Producent będzie wysyłał komunikaty z nagłówkiem, który będzie użyty to stworzenia nazwy kolejki klienta. Wewnątrz będziemy używać [Apache Camel](http://camel.apache.org), który będzie przetwarzał wiadomości przychodzące i rozsyłał je do kolejek konkretnych klientów. Camel będzie również kopiował komunikaty do kolejki z logami dla procesu audytującego (niewymieniony na diagramie).
[![](/wp-content/uploads/2011/06/mom_architecture.png)](/wp-content/uploads/2011/06/mom_architecture.png)
Jako alternatywę zamierzam wam pokazać jak pobrać identyfikator klienta z treści komunikaty zamiast zmuszać producenta do używania nagłówków.

## Stawianie ActiveMQ

Jeśli jesteś użytkownikiem Linuxa bądź MacOSa możesz użyć menadżera paczek [brew](https://github.com/mxcl/homebrew) by zainstlować activemq następującym poleceniem:
```bash
brew install activemq
```
Pozostali użytkownicy mogą pobrać dystrybucję ActiveMQ ze [strony projektu](http://activemq.apache.org/).

Mamy zainstalowane ActiveMQ, w następnym kroku stworzymy testową instancję brokera, którą wykorzystamy do modyfikacji konfiguracji. Aby stworzyć instancję należy wywołać polecenie:
```bash
activemq-admin create mom
```
Po wykonaniu tego polecenia powinniście otrzymać następującą strukturę katalogów
[![](/wp-content/uploads/2011/06/mom_broker.png)](/wp-content/uploads/2011/06/mom_broker.png)

Nie będziemy używać wszystkich plików, zatem w moim setupie zostaną usunięte następujące pliki:

- Ponieważ jeszcze nie używamy SSL:
  - broker.ks
  - broker.ts
- Ponieważ nie zabezpieczamy brokera:
  - credentials.properties
- Ponieważ nie będziemy używać konsoli webowej:
  - jetty-realm.properties
  - jetty.xml

Te pliki są niepotrzebne w tej chwili w naszej instalacji.

## Konfiguracja ActiveMQ

Ponieważ skrypt activemq-admin tworzy domyślną konfigurację brokera będziemy musieli ją nieco zmodyfikować. **Pamiętaj aby wstawiać elementy w XML w porządku alfabetycznym. Od wydania 5.4 elementy w konfiguracji ActiveMQ są uporządkowane.**. Na początku zmienimy element destinationPolicy:
```xml
<destinationPolicy>
    <policyMap>
        <policyEntries>
            <policyEntry queue=">" optimizedDispatch="true" lazyDispatch="false" producerFlowControl="false" memoryLimit="12 mb" />
            <policyEntry queue="MOM.Client.>" advisoryForDelivery="true" advisoryForConsumed="true" />
        </policyEntries>
    </policyMap>
</destinationPolicy>
```
Pierwszy element policyEntry wyłącza producerFlowControl. Innymi słowy ActiveMQ będzie akceptować wszystkie wiadomości wysłane przez producenta nawet po przekroczeniu limitu pamięci. Komunikaty które nie będą mieściły się w pamięci będą zapisywane (na dysku). Domyślnie dyrektywa producerFlowControl w ActiveMQ jest ustawiona na true i broker będzie informował producenta by poczekać na zwolnienie pamięci, np. na to by komunikaty z kolejki zostały skonsumowane. Drugi element włącza komunikaty pomocnicze dla dostarczenia i konsumpcji komunikató∑ z kolejek MOM.Client.\*. Komunikaty te są wysyłane przez brokera gdy komunikat od producenta został przyjęty i pomyślnie przetworzony przz konsumenta. ActiveMQ oferuje kila [komunikatów i topiców pomocniczych](http://activemq.apache.org/advisory-message.html). Niektóre z nich są domyślnie wyłączone, inne włączone.

Druga zmiana to dodanie w konfiguracji pre definiowanych destynacji.
```xml
<destinations>
    <queue physicalName="MOM.Incoming" />
    <queue physicalName="MOM.Audit" />
</destinations>
```

Owe destynacje zostaną stworzone podczas uruchomienia brokera, nawet jeśli nie będzie konsumentów i producentów. Po tych zmianach możemy wystartować naszego brokera następującym poleceniem:
```bash
$ACTIVEMQ_HOME/bin/mom console
```
Z poziomu VisualVM możemy sprawdzić które destynacje zostały stworzone. Po uruchomieniu narzędzia monitorującego po prostu wybieramy lokalny proces org.apache.activemq.console.Main, który powinien być wyświetlony po wykonaniu powyższego polecenia.
[![](/wp-content/uploads/2011/06/mom_jmx.png)](/wp-content/uploads/2011/06/mom_jmx.png)

To wszystko jeśli idzie o ustawienia ActiveMQ. Jeśli trafiłeś na problemy ze zmianami w konfiguracji możesz je pobrać bezpośrednio z [repozytorium git](https://github.com/splatch/mom-sample) bądź pobrać [archiwum zip](https://github.com/splatch/mom-sample/zipball/master) \| [archiwum tar](https://github.com/splatch/mom-sample/tarball/master) w przypadku gdy nie masz klienta git.

- **Część 1: Building sample middleware - broker**
- Część 2: Building sample middleware - struktury danych
- Część 3: Building sample middleware - producent
- Część 4: Building sample middleware - camel
- Część 5: Building sample middleware - konsument
- Część 6: Building sample middleware - audyt

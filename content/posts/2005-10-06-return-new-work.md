---
author: splatch
category:
  - ogólne
date: "2005-10-06T11:12:34+00:00"
guid: http://blog2.dywicki.pl/2005/10/06/return-new-work/
title: return new Work;
url: /2005/10/06/return-new-work/

---
No.. siedzę właśnie w nowej pracy. Jest super. Ludzie spoko, szef w porządku - słowem pracować i nie odchodzić. Do nowego miejsca pracy mam nieco ponad 10 minut pieszo, więc odchodzi problem z autobusami (ostatnio tyle czasu potrzebowałem na dojście do przystanku). Pojawił się mały problem, bo straciłem hasło do kompa w domu i jak by nie patrzećjestem uziemiony. Prace nad PSF stoją, a ja kwitnę wieczorami przed telewizorem. Ale nie będzie źle ... po reinstalacji systemu pewnie będzie troszkę szybciej działać. W sumie, może w grudniu wezmę laptopa w raty ...

Co do PSF naszkicowałem diagramy klas dla filtrów i configów. Ten pierwszy nie różni się niczym od tego, który można zobaczyć w Core J2EE patterns przy custom filter strategy.

Wcześniej zarzucano mi definiowanie na sztywno ścieżek do plików konfiguracyjnych. Wpadłem na pomysł jak to rozwiązać:

```
<module>         <name>JakiśMod</name>         <default>JakaśAkcja</default>         <config>%PSF_DIR%/Modules/%PSF_MODULE_NAME%/Config.xml</config>     </module>
```

Myślę, że rozwiąże to częściowo problem z definiowaniem typu parsera..

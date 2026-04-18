---
author: splatch
category:
  - sprzęt
  - śmieci
date: "2008-02-02T20:34:20+00:00"
guid: http://blog.dywicki.pl/2008/02/02/aktualizacja-sterownikow-dla-kart-ati/
summary: |-
  Jakiś czas temu nieszczęśliwym zbiegiem okoliczności straciłem możliwość uruchomienia swojego systemu (XP). Winny całej sytuacji był partition magick, który nie poradził sobie z przeskalowaniem partycji. Koniec końców spod suse zrobiłem backup danych i postanowiłem zainstalować XP raz jeszcze. Niestety płytka, na której był nagrany (wersja z MSDNAA), nie podnosiła się. Byłem zmuszony do skorzystania z instalki Visty. Kosztowała mnie ona 30 zł i to w cale nie na, zamkniętym już wtedy, stadionie dziesięciolecia! Asus organizował coś takiego jak Visa upgrade i rozdawał płytki za darmo. Swoją dostałem w maju.
  Koniec końców nowe dziecko MS wylądowało na moim twardym dysku. Kolorki kolorkami, zasoby zasobami, ale najgorsze było to, że straciłem możliwość grania w Enemy Territory. Ta stara gra chodziła mi na XP bardzo płynnie, niestety na Viście nie osiągałem więcej niż 30 fps. Zrozpaczony tym zrezygnowałem z pogrywania. Jakiś czas później naszła mnie chęć na aktualizację sterowników do karty graficznej, ponieważ wszystko co było oparte o OpenGL chodziło tak jak by chciało a nie mogło. Udałem się na stronę [ATI](http://ati.amd.com/) by pobrać sterowniki. Jakież było moje zdziwienie, gdy producent oznajmił, że do kart OEM sterowniki powinni zapewnić ich producenci. W tym momencie praktycznie wszystkie układy zbudowane w oparciu o kości Radeon montowane w laptopach zatrzymują się na wersji sprzed roku, jeśli nie starszej.
  Zaintrygowany tym zacząłem szperać i znalazłem rozwiązanie. :)
title: Aktualizacja sterowników dla kart ATI
url: /2008/02/02/aktualizacja-sterownikow-dla-kart-ati/

---
Jakiś czas temu nieszczęśliwym zbiegiem okoliczności straciłem możliwość uruchomienia swojego systemu (XP). Winny całej sytuacji był partition magick, który nie poradził sobie z przeskalowaniem partycji. Koniec końców spod suse zrobiłem backup danych i postanowiłem zainstalować XP raz jeszcze. Niestety płytka, na której był nagrany (wersja z MSDNAA), nie podnosiła się. Byłem zmuszony do skorzystania z instalki Visty. Kosztowała mnie ona 30 zł i to w cale nie na, zamkniętym już wtedy, stadionie dziesięciolecia! Asus organizował coś takiego jak Visa upgrade i rozdawał płytki za darmo. Swoją dostałem w maju.
Koniec końców nowe dziecko MS wylądowało na moim twardym dysku. Kolorki kolorkami, zasoby zasobami, ale najgorsze było to, że straciłem możliwość grania w Enemy Territory. Ta stara gra chodziła mi na XP bardzo płynnie, niestety na Viście nie osiągałem więcej niż 30 fps. Zrozpaczony tym zrezygnowałem z pogrywania. Jakiś czas później naszła mnie chęć na aktualizację sterowników do karty graficznej, ponieważ wszystko co było oparte o OpenGL chodziło tak jak by chciało a nie mogło. Udałem się na stronę [ATI](http://ati.amd.com/) by pobrać sterowniki. Jakież było moje zdziwienie, gdy producent oznajmił, że do kart OEM sterowniki powinni zapewnić ich producenci. W tym momencie praktycznie wszystkie układy zbudowane w oparciu o kości Radeon montowane w laptopach zatrzymują się na wersji sprzed roku, jeśli nie starszej.
Zaintrygowany tym zacząłem szperać i znalazłem rozwiązanie. :)

1\. Ściągamy [ModTool](http://www.driverheaven.net/modtool/)
2\. Być może konieczna będzie aktualizacja [MS XML](http://www.microsoft.com/downloads/details.aspx?familyid=24B7D141-6CDF-4FC4-A91B-6F18FE6921D4&displaylang=en)
3\. Następnie udajemy się po najnowsze sterowniki na stronę ATI bądź [DriverHeaven](http://www.driverheavendownloads.net/ati.htm)
4\. Po ściągnięciu Catalyst będzie chciało się rozpakować do jakiegoś katalogu (C:\\ati\\support) - niech wyląduje gdzie chce, jakkolwiek nie uruchamiajcie instalacji
5\. Uruchamiany ModTool i wskazujemy lokalizację C:\\ati\\support, następnie potwierdzamy, że chcemy te sterowniki zmodyfikować
6\. Po zmodyfikowaniu instalujemy standardowo Catalyst
7\. Jeśli Windows będzie ostrzegał, że sterowniki nie są podpisane należy zignorować komunikat

Po tymże zabiegu Enemy Territory wyciąga standardowo pomiędzy 60-100 fps na wysokich detalach.
Dziwne jest zachowanie ATI/AMD. Całe szczęście są jeszcze na świecie ludzie z głowami na karku, którzy potrafią naprawić błędy innych. :)

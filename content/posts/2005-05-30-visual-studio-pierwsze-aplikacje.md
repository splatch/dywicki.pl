---
author: splatch
category:
  - .net
date: "2005-05-30T21:22:43+00:00"
guid: http://blog2.dywicki.pl/2005/05/30/visual-studio-pierwsze-aplikacje/
summary: |-
  Nadszedł ten czas - pierwsze "poważne" boje ze studiem. Postanowiłem zacząć od aplikacji konsolowych, ponieważ jest przy nich najmniej problemów, nie ma otoczki graficznej, formularzy, zasobów, jest tylko sam kod. Z resztą trzy miesiące temu udało mi się ściągnąć VS 2005 C# Express i troszkę się nim nacieszyłem (czyt. do formatu). Wtedy to właśnie udało mi się napisać (a bardziej zrobić) program okienkowy który ładował dane do XMLa. Nie było to nic ambitnego, ponieważ wszytko odbywało się poprzez (poniekąd) ręczne wpisanie kodu do pliku (czyli +"zmienna+" i tak dalej). Słowem nic specjalnego, zakręcony formularz i zero funkcjonalności.

  Tym razem moje ambicje sięgały wyżej niż witaj świecie. Chciałem napisać witaj łukaszu! :) Ten jakże odległy szczyt udało mi się osiągnąć dwiema podobnymi drogami - przy pomocy J# i C#. Podstawy Javy mam (nikłe bo nikłe ale są), jak wyżej pisałem C# to też dla mnie nie pierwszyzna ;) poszło dość szybko. Niestety nie dałem rady zrobić tego w VB i C++, ale jest czas.

  Zacząłem od C#. Pamiętałem, żeby nie używać strzałeczek bo to nie php. Trzeba było się przesiąść na kropeczki. Pamiętałem że interesująca mnie klasa (?) to **System.Console**. No trudno byłoby zapomnieć - już na samym początku została dołączona. Stąd było już blisko ReadLine, Write i znowu Read. W najbliższym czasie w programie nauczania C# liczenie pola i obwodu prostokąta.

  Z J# poszło mi szybciej niż myślałem. Nazewnictwo metod identyczne, jedyna różnica to początek programu. W dalszej części do oglądnięcia kody programów.

  Książkę do ASP mam.. tylko, że ASP Kompendium to nie jest tytuł pierwszej świeżości. Będzie trzeba odłożyć na jakiś porządny tytuł (ostatnie pieniądze poszły na świętowanie z okazji zdania matury).
title: Visual Studio - pierwsze aplikacje
url: /2005/05/30/visual-studio-pierwsze-aplikacje/

---
Nadszedł ten czas - pierwsze "poważne" boje ze studiem. Postanowiłem zacząć od aplikacji konsolowych, ponieważ jest przy nich najmniej problemów, nie ma otoczki graficznej, formularzy, zasobów, jest tylko sam kod. Z resztą trzy miesiące temu udało mi się ściągnąć VS 2005 C# Express i troszkę się nim nacieszyłem (czyt. do formatu). Wtedy to właśnie udało mi się napisać (a bardziej zrobić) program okienkowy który ładował dane do XMLa. Nie było to nic ambitnego, ponieważ wszytko odbywało się poprzez (poniekąd) ręczne wpisanie kodu do pliku (czyli +"zmienna+" i tak dalej). Słowem nic specjalnego, zakręcony formularz i zero funkcjonalności.

Tym razem moje ambicje sięgały wyżej niż witaj świecie. Chciałem napisać witaj łukaszu! :) Ten jakże odległy szczyt udało mi się osiągnąć dwiema podobnymi drogami - przy pomocy J# i C#. Podstawy Javy mam (nikłe bo nikłe ale są), jak wyżej pisałem C# to też dla mnie nie pierwszyzna ;) poszło dość szybko. Niestety nie dałem rady zrobić tego w VB i C++, ale jest czas.

Zacząłem od C#. Pamiętałem, żeby nie używać strzałeczek bo to nie php. Trzeba było się przesiąść na kropeczki. Pamiętałem że interesująca mnie klasa (?) to **System.Console**. No trudno byłoby zapomnieć - już na samym początku została dołączona. Stąd było już blisko ReadLine, Write i znowu Read. W najbliższym czasie w programie nauczania C# liczenie pola i obwodu prostokąta.

Z J# poszło mi szybciej niż myślałem. Nazewnictwo metod identyczne, jedyna różnica to początek programu. W dalszej części do oglądnięcia kody programów.

Książkę do ASP mam.. tylko, że ASP Kompendium to nie jest tytuł pierwszej świeżości. Będzie trzeba odłożyć na jakiś porządny tytuł (ostatnie pieniądze poszły na świętowanie z okazji zdania matury).

## J\#

```
  package PierwszaAplikacja; public class Program {     public static void main(String[] args) {         System.Console.Write("Wpisz swoje imie: ");         String text = System.Console.ReadLine();         System.Console.Write("Witaj " + text + "!\n" +             "Wcisnij dowolny klawisz aby zakonczyc dzialanie aplikacji");         System.Console.ReadKey();     } }  C#  using System; namespace PierwszaAplikacja {     class Program {         static void Main(string[] args) {             System.Console.Write("Wpisz swoje imie: ");             String text = System.Console.ReadLine();             System.Console.Write("Witaj "+text+"\n"+                 "Wcisnij dowolny klawisz aby zakonczyc dzialanie aplikacji");             System.Console.ReadKey();         }     } }
```

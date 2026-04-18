---
author: splatch
category:
  - php
date: "2005-09-22T10:48:04+00:00"
guid: http://blog2.dywicki.pl/2005/09/22/optimization/
title: O::ptimization
url: /2005/09/22/optimization/

---
Jako, że prace nad frameworkiem troszkę się posunęły (doszło uruchamianie akcji) postanowiłem sprawdzić ile czasu zajmuje jedno ządanie. Jakież było moje zaskoczenie, gdy mym oczom ukazał się czas ponad 1 sekundy. Szybko sobie jednak przypomiałem, że autoloader jest do wymiany. Przebudowałem stary tak, aby współpracował z tokenizerem beli. Zmiana – względem poprzedniego czasu – znaczna. Czas spadł do około 0.2 s. Kilka poprawek i zszedłem poniżej – teraz czas jednego rządania wynosi od 0.12 do 0.15 s. Stwierdziłem, że to strasznie długo tym bardziej, że nie ma jeszcze obsługi kilku istotnych rzeczy (FilterChain, Validator, View, ActionForms). Wniosek? Błędy w projekcie. Oto porównanie z innymi skryptami, które udało mi się odanleść na dysku:

**Mój framework:**  

czas od 0.12 do 0.15 – 33 włączone pliki.

**Odin:**  

czas od 0.047 do 0.063 – 23 włączone pliki (w tym Smarty.class.php)

**Blog+ (Pinky):**  

czas od 0.24 do 0.26 - 58 włączonych plików (plus połączenie z MySQL)

**Daeron:**  

czas od 0.92 do 0.96 – 15 włączonych plików (PDO zgłasza wyjątek)

**Cake:**  

czas od 0.11 do 0.14 – 28 włączonych plików (troszkę niedziałająca wersja)

**Mojavi 3.0:**  

czas od 0.16 do 0.23 – 41 włączonych plików (PHPView, bez połączenia z bazą)

Co dziwne – najmniej włączający Daeron okazał się znacznie wolniejszy od reszty. Za to kolejny pod względem liczby dołączanych plików Odin najszybszy. Przypadek? Trudno powiedzieć, ponieważ wszystkie testowane frameworki/aplikacje są w różnym stadium rozwoju. Obrazowo – Odin szybszy od Daerona ponad 19 razy, a od drugiego w testach Cake'a o ponad 2 razy. Stosunek czasów Odina do Blog+ i Mojavi to 5 i 3. Podkreślam, że testy były przeprowadzane manualnie i pomyłki są bardzo prawdopodobne. Testy przeprowadziłem na swoim domowym sprzęcie: Athlon 2000 XP (1.67 Ghz), 768 MB RAM (DDR 400), OS: Windows XP HE, Apache 1.3.33 i PHP 5.1.0 b2 (z PDO i XSL). W tle działały wciąż te same aplikacje.

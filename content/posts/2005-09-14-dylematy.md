---
author: splatch
category:
  - php
date: "2005-09-14T11:09:09+00:00"
guid: http://blog2.dywicki.pl/2005/09/14/dylematy/
title: Dylematy.
url: /2005/09/14/dylematy/

---
![kliknij aby powiększyć](http://splatch.php.org.pl/_img/dev_m.gif) Tak to już jest, że coraz większość problemów pojawia się przy detalach... Również i w przypadku tego, co piszę obecnie pojawiła się garść problemów.

Pierwszy problem to zrezygnowanie z buforowania plików konfiguracyjnych. Do operowania na XMLu wykorzystuję XPatha, a ten jest dostępny poprzez DOM bądź SimpleXML. Podejrzewam, że tak czy owak SimpleXML przerzuca swój obiekt do obiektu DOM, żeby wykonać xpath\_eval, ale nie jestem w stanie tego udowodnić. Jak wielkie spowolnienie z tego wyniknie? Nie jestem w stanie powiedzieć. Bez buforu konfiguracji jedno żądanie zajmuje około 0.05s. Gdy tworzona jest mapa klas czas ten wzrasta ponad dziesięciokrotnie.

No, ale jakoś sobie poradzę. Mogę tam, gdzie SimpleXML jest konwertowany do tablicy zastosować warunek, który będzie sprawdzał czy w pliku zostały wprowadzone zmiany. Problem drugi to łańcuchy. Kolega z pracy, który wiele pracował ze Strutsem zaproponował, żeby nie implementować łańcuchów tylko obsługę forwardów tak jak w wyżej wymienionym frameworku.

W sumie nie jest to problem, stworzę obiekt ForwardConfig, który będzie wczytywał dane z forwardami lokalnymi i globalnymi dla całego modułu/aplikacji.

Walczyłem również z obsługą Requestu.Tzn. problem pojawił się, gdy postanowiłem rozdzielić odpowiedzialność na dwie klasy. Console oraz Web. Ostatecznie, aby wiedzieć skąd pochodzi zmienna w przypadku korzystania z obiektu WebRequest nadpisałem metodę

Request::getParameter. Teraz przyjmuje dwa argumenty zamiast jednego. Pierwszy to nazwa argumentu, drugito miejse, skąd ma pochodzić zmienna. Jest to numer – odpowiednio 0 dla COOKIE, 1 dla GET, 2 dla POST, 3 dla FILES. Domyślnie drugi argument przyjmuje wartość 1, czyli dane będą pobierane z GETa.

Obecnie cały czas walczę nad skończeniem Controllera. Wtedy będę mógł zająć się klasą ApplicationController, która będzie miała za zadanie obsługiwać moduły.  

No a najcięższy kawałek chleba zapowiada się przy XML2Form. Myślę, że za około miesiąc, może 2, gdy całość zacznie jako-tako funkcjonować uwolnię źródła.

Dzisiaj udało mi się również uruchomić SimpleTest. Być może dzisiaj napiszę kilka testów do frameworka. Wczoraj straciłem ponad 2 godziny na próbę uruchomienia Propela. To kurewskie środowisko jest nie do uruchamienia. Tym, bardziej, że bez dostępu do internetu w domu nie mogę ściągnąć wersji z SVN, a bez tego po prostu klapa. Teoretycznie wykonuję wszystko tak jak jest w dokumentacji, ale w praktyce nie są generowane klasy, bo pojawia się wszędobylski błąd 'Class File not found'. Wg. wskazówek, które są na stronie powinno starczyć tylko dodanie w jednym pliku Phing do linii, ale nie pomaga.

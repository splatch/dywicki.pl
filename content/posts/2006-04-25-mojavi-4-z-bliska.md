---
author: splatch
category:
  - framework
  - mojavi4
  - mvc
  - php
date: "2006-04-25T21:22:08+00:00"
guid: http://blog.dywicki.pl/2006/04/25/mojavi-4-z-bliska/
summary: |-
  Tak jak obiecałem informacje na temat zmian w Mojavi 4.
  Mojavi jest jednym ze starszych frameworków dla PHP. Swoje korzenie ma jeszcze w PHP4 kiedy to powstała pierwsza (na chwilę obecną), ostatnia stabilna wersja – Mojavi 2.0. Po wejściu PHP5 okazało się, że M2 nie jest kompatybilne z nowym interpreterem. Zaczęło powstawać Mojavi 3. Istnieje również Yuma – Mojavi 2 zgodne z PHP5. W pewnym momencie grupa (a właściwie jedyna osoba) tworząca Mojavi zdecydowała, że projekt jest nienajlepszy. Mimo wszystko była to tylko kontynuacja M2, bez wykorzystania możliwości PHP5, oparta na klasach. M3 zostało wstrzymane a Sean Kerr (na forum illusina) zaczął tworzyć Mojavi 4. M3 utkęło w fazie DEV. Mimo wszystko framework jest bardzo stabilny, sam z niego korzystałem kilka razy i nie było problemów. Wszystko czego potrzeba było to tylko kilka komponentów, które można pobrać z sieci (między innymi SmartyView). Aby praca włożona w M3 nie poszła na daremne projekt przejął Tyler Tompkins. Tak powstało Agavi. Agavi oferuje dodatkową funkcjonalność, usuwa niektóre problemy z Mojavi (min. problem z nazwami klas), oferuje również wbudowane komponenty, których brakowało Mojavi:
  \- SmartyView
  \- XSLTView
  \- CreoleDatabase
  \- PropelDatabase
  \- AdoDBDatabase
  \- PDODatabase
  \- ukończony mechanizm logowania
  \- zaczątki modelu (na forum Agavi jest do pobrania ActiveRecord rodem z Cake/Rails)
  \- ReturnArrayConfigHandler
  \- ConsoleController
  \- ConsoleRequest
  \- HTTPSRequest
  \- CreoleStorage
  \- wsparcie dla Phinga – taski do tworzenia projektu, modulu, akcji, widoku, unit-testów).
  \- bodajże DateValidator
  \- FormPHPView (ma być w krótce usunięty).
title: Mojavi 4, z bliska.
url: /2006/04/25/mojavi-4-z-bliska/

---
Tak jak obiecałem informacje na temat zmian w Mojavi 4.
Mojavi jest jednym ze starszych frameworków dla PHP. Swoje korzenie ma jeszcze w PHP4 kiedy to powstała pierwsza (na chwilę obecną), ostatnia stabilna wersja – Mojavi 2.0. Po wejściu PHP5 okazało się, że M2 nie jest kompatybilne z nowym interpreterem. Zaczęło powstawać Mojavi 3. Istnieje również Yuma – Mojavi 2 zgodne z PHP5. W pewnym momencie grupa (a właściwie jedyna osoba) tworząca Mojavi zdecydowała, że projekt jest nienajlepszy. Mimo wszystko była to tylko kontynuacja M2, bez wykorzystania możliwości PHP5, oparta na klasach. M3 zostało wstrzymane a Sean Kerr (na forum illusina) zaczął tworzyć Mojavi 4. M3 utkęło w fazie DEV. Mimo wszystko framework jest bardzo stabilny, sam z niego korzystałem kilka razy i nie było problemów. Wszystko czego potrzeba było to tylko kilka komponentów, które można pobrać z sieci (między innymi SmartyView). Aby praca włożona w M3 nie poszła na daremne projekt przejął Tyler Tompkins. Tak powstało Agavi. Agavi oferuje dodatkową funkcjonalność, usuwa niektóre problemy z Mojavi (min. problem z nazwami klas), oferuje również wbudowane komponenty, których brakowało Mojavi:
\- SmartyView
\- XSLTView
\- CreoleDatabase
\- PropelDatabase
\- AdoDBDatabase
\- PDODatabase
\- ukończony mechanizm logowania
\- zaczątki modelu (na forum Agavi jest do pobrania ActiveRecord rodem z Cake/Rails)
\- ReturnArrayConfigHandler
\- ConsoleController
\- ConsoleRequest
\- HTTPSRequest
\- CreoleStorage
\- wsparcie dla Phinga – taski do tworzenia projektu, modulu, akcji, widoku, unit-testów).
\- bodajże DateValidator
\- FormPHPView (ma być w krótce usunięty).

W międzyczasie, kiedy powstawało Agavi pojawił się kolejny projekt, który nadrabia największy brak Mojavi i póki co również Agavi.. dokumentację. Chodzi tutaj oczywiście o Symfony. Znowu dodatkowe komponenty, wbudowane I18n, pełna integracja z Propelem i Phingiem (generowanie akcji na podstawie struktury bazy).

To co mnie jednak najbardziej inspiruje to zmiany w M4, a są one ogromne. Na pierwszy ogień idzie całkowity redesign frameworka, który wg. zapowiedzi autorów ma być na tyle elastyczny by tworzyć w oparciu o niego inne, wyspecjalizowane frameworki. Jak to padło na forum “Framework for frameworks”.

Bardzo dobrym pomysłem w M4 jest Service Locator. Nie jest to co prawda SL który znam z definicji w PoEAA, ale spełnia podobne zadanie i znacznie uelastycznia budowę aplikacji. Wcześniej, gdy tworzyłem I18n dla Agavi musiałem grzebać po kodzie by dodać jego obsługę. Teraz wystarczy dodać do services.xml odpowiednie linijki i mamy już usługę, do której możemy się odwołać poprzez Service Locatora. Jest to swojego rodzaju kontener IoC, który znalazł swoje zastosowanie w projektach takich jak Spring (framework dla aplikacji pisanych w Javie).

Kolejną wielką zmianą, która na pewno wyjdzie projektowi na dobre jest zastosowanie Application Controllerów. Podobny mechanizm jest w Springu i przyznam, że bardzo mi się spodobał. Daje on potężne możliwości oraz ułatwia tworzenie akcji. Teraz nie musimy już się męczyć z tworzeniem formularzy, które chcemy realizować na kilku stronach – mamy od tego Step Application Controllera. Co prawda kolekcja kontrolerów jest jeszcze bardzo skromna (prócz wcześniej wymienionego nie ma innego rozszeżenia ApplicationControllera) ale liczę, że z czasem zacznie ich przybywać. Wreszcie akcje będą ciekawsze. ;)

Totalnie przebudowany został mechanizm obsługujący wywoływanie widoków. Dzięki temu unikniemy problemów z dużą ilością powtarzającego się kodu w bardzo wielu plikach. W Mojavi 3 jeden widok = jedna klasa. W czwórce jeden widok to jedna metoda.

Kolejna nowość to typy widoków. Wcześniej by stworzyć widok, który wyrzucał dane w innym formacie (np generował plik pdf) trzeba było tworzyć kolejną akcję i kolejne klasy widoku. W Mojavi 4 można zmieniać typ outputu przy pomocy przełącznika ctype, który może wędrować również po adresie. Dzięki temu nie ma potrzeby dublowania kodu akcji. W nieznacznym stopniu powtarza się kod widoku. Schemat nazwy pliku to View.class.php. Po kilku chwilach walki udało mi się stworzyć moduł który w bardzo sprawny sposób pobierał fragmenty strony poprzez Ajax. Myślę, że z udostępnianiem danych via JSON dla JS nie byłoby również problemu.

Niejako spełnieniem oczekiwań jest wprowadzenie Layout-ów. W M3 elementy dynamiczne wchodzące w skład widoku były ustawiane poprzez setSlot. Gdy trzeba było dodać w całej aplikacji nowy blok konieczne były zmiany we wszystkich widokach. Teraz każda klasa widoku zwraca odpowiedni layout, który w gruncie rzeczy jest odseparowanym fragmentem kodu z informacjami na temat tego jakie komponenty mają być użyte. Teraz tzw. bloki mogą być również oddzielnymi szablonami. Wcześniej każdy blok musiał mieć swoją akcję, obecnie wystarczy odpowiednie wykorzystanie registerComponent.

W czwórce możemy również w obrębie jednej klasy widoku korzystać z różnych rendererów. Wcześniej to przez jaki renderer przechodził szablon było uzależnione od tego z jakiej klasy dziedziczył widok, obecnie każdy widok może korzystać z różniastych klas (vide SmartyRenderer, PHPRenderer etc.) a zatem różne fragmenty stron, jeśli zajdzie taka potrzeba mogą być generowane różnymi technikami.
Reasumując. Mojavi 4 to na pewno krok w dobrą stronę. Framework zatrzymuje to, co było najlepsze w poprzednich wersjach równocześnie dorzucając gro przydatnych nowości, które na pewno umilą tworzenie aplikacji w PHP. Sam obserwuję SVN z wypiekami na twarzy by wyłapać wszystkie zmiany jakie zachodzą. Mimo, że w PHP w chwili obecnej piszę na prawdę niewiele to nie mogę się doczekać wydania chociażby bety. M4 polecam waszej uwadze.

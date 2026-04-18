---
author: splatch
category:
  - db
date: "2006-06-25T08:08:50+00:00"
guid: http://blog.dywicki.pl/2006/06/25/oracle-xe/
title: Oracle XE
url: /2006/06/25/oracle-xe/

---
![Oracle #4](/wp-content/uploads/2006/06/ora4.thumbnail.JPG)Niedawno postanowiłem zaryzykować i ściągnąć Oracle XE. Samo ściągnięcie nie trwało dość długo (w międzyczasie sobie jadłem kolację), w końcu to tylko 150 MB. Zapewne niektórym w tym miejscu trudno uwierzyć, że tylko tyle. Tak tylko tyle! ;) Fakt, w porównaniu z MySQL/PgSQL to naprawdę wiele, ale nie w porównaniu do pełnego pakietu Oracle, który waży znacznie więcej. Tak więc w mych rękach wylądowała wersja express Oracle 10, która była gotowa do instalacji. Procesu instalacji nie opisywałbym gdyby nie to, że minimalna ilość pamięci to ponad 256 MB. Zatem niestety, albo i stety nie mogłem zainstalować bazy na laptopie. Nie mniej, podczas pobytu w domu z powodzeniem instalację udało mi się zakończyć. Co mnie zaskoczyło – nie zmuliło mi od razu systemu, jak to ongi bywało. Pamiętam, że gdy pierwszy raz instalowałem Oracle (rozmiar ~500 MB) to system dosłownie stawał w miejscu. Dlaczego? Ponieważ przy normalnej instalacji Oracle używa kilku i więcej procesów pomocniczych, także nie jest to tak jak w przypadku MySQL jeden proces. Dzięki temu baza o wiele lepiej sprawuje się na maszynach wieloprocesorowych.
Podstawowe procesy pomocnicze to:
\- dbw – proces zapisujący do plików danych – procesów tych może być do 9 (dbwr0.. dbwr9)
\- pmon – monitor procesów
\- smon – monitor systemu
\- lgwr – proces zapisujący do plików dziennika powtórzeń
\- ckpt – proces punktu kontrolnego
\- reco – odtwarzacz
\- arc0 – archiwizacja danych
![Oracle #3](/wp-content/uploads/2006/06/ora3.thumbnail.JPG)W przypadku wersji express tych procesów nie ma, jest jeden główny, który sobie spokojnie radzi ze wszystkim. Nie mam pojęcia jak to jest realizowane, ale na systemie XP, który ma ponad pół roku, gdzie dysk naprawdę jest zawalony po brzegi i gdyby nie zakładki na tychże to by się z niego wylewało baza działała szybko a co najważniejsze nie destabilizowała systemu jak i pracy innych aplikacji. Dla mnie bomba, proces nie zżerał całej dostępnej pamięci, tak że spokojnie mogłem przy 768 MB pamięci pracować jeszcze na Eclipse. Nie do wiary? A jednak, dało radę. W tym przypadku śmiem twierdzić, że gadanie "Oracle zmula" są mocno nieaktualne. Nie zmula! Oczywiście wciąż mówimy cały czas o wersji express, która w sam raz nadaje się do testów a nie o instalacji w pełni funkcjonalnej.
![Oracle #2](/wp-content/uploads/2006/06/ora2.thumbnail.JPG)Cóż takiego ciekawego jest w tym oprogramowaniu?
1\. Po pierwsze, dlaczego powstają książki rozmiarowo przewyższające encyklopedie na jego temat.
2\. Po drugie czy nie jest to wodolejstwo.
3\. Po trzecie czy możliwości są adekwatne do ceny.

Ad. 1) Nie jestem w stanie tego jednoznacznie stwierdzić, ale odnośnie opasłych tomisk – pragnę przytoczyć tytuł "Oracle. Vademecum administratora" (jeśli dobrze pamiętam).
Obecnie w swej skromnej biblioteczce posiadam tytuł "Oracle 9i. Przewodnik początkujących", który jest stosunkowo niewielki – około 420 stron. Jest to mniej niż choćby "PostgreSQL. Biblia problemów". Nie ma jednak w niej przedruku z całą składnią SQL jaką baza obsługuje. Jest dużo teorii, sporo opisów. Rozszerzeniem lektury jednak może być Vademecum jak i "Oracle 9i i XML" (znowu, jeśli dobrze pamiętam). W sumie wychodzi o wiele więcej niż na temat innej bazy danych, chociaż może tutaj konkurować jeszcze Microsoft SQL Server.
Ad. 2) Myślę, że wodolejstwo to nie jest, ponieważ sama baza danych daje ogromne możliwości, które ciężko bez dobrych podstaw wykorzystać. Mówię tutaj o czymś takim jak partycjonowanie danych czy też hurtownie danych. Dla mnie jest to swoista nowość, nigdy wcześniej z tymi pojęciami się nie spotkałem.
![Oracle #1](/wp-content/uploads/2006/06/ora1.thumbnail.JPG)Ad. 3) Czy warto? Zależy, najlepszą metodą na sprawdzenie tego są samodzielne testy, ponieważ w sieci brak porównań wydajnościowych (Oracle zabrania jakichkolwiek publikacji na ten temat) a nie ma dwóch takich samych sytuacji nie mówiąc już o potrzebach. Wiele osób chce tego samego, ale różnice tkwią w szczegółach. Sądzę, że funkcjonalnie jest to najlepsza baza danych, którą Microsoft dopiero goni i do której ma jeszcze kawał drogi. Są to różnice lat. Podczas gdy Bill rozwijał swój sztandarowy produkt Oracle wciąż pracowało nad bazą danych i aplikacjami mocno związanymi z jej działaniem.

Sam przyznam, że niedawno odpaliłem tą bazę pierwszy raz i nie mogę znać ogromu jej możliwości. Jestem na wierzchołku góry lodowej. Z ciekawszych możliwości – bawiłem się formularzami oraz arkuszami (chyba tak to się zwie). Przyznam, że fajne. To, do czego dążą chyba wszyscy programiści – "kliknij tutaj by stworzyć aplikację" – jest już w Oracle. Nie mam pojęcia jak wygląda to w przypadku specyficznych struktur i tak dalej, ale na tych tabelach, które są dostępne dla testowego użytkownika wszystko sprawowało się znakomicie. Można sobie edytować bądź dodawać przyciski, schematy, szablony i co tam sobie jeszcze kto wymyśli. Wszystko przez interfejs WWW, bez używania narzędzi ciężkiego kalibru. Myślę, że załączone zrzuty są wystarczającą referencją i w pełni zastąpią wszelkie opisy..
Zachęcam do zabawy z Oracle, ono nie gryzie! :)
Udało mi się uruchomić bez jakiegokolwiek wysiłku PDO i z poziomu PHP korzystać z zasobów bazy. Troszkę się namęczyłem z Creole ale udało mi się również je zmusić do pracy z bazą. Być może pora na stworzenie adaptera, który rozszerzy interfejs PDO do tego, który oferuje Creole?

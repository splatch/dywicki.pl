---
author: splatch
category:
  - php
date: "2007-02-11T19:41:36+00:00"
guid: http://blog.dywicki.pl/2007/02/11/propel-12-a-istniejaca-baza-danych/
title: Propel 1.2 a istniejąca baza danych
url: /2007/02/11/propel-12-a-istniejaca-baza-danych/

---
Wiele razy spotykałem się z negatywnymi opiniami na temat Propela. Przyznaję, nie jest to narzędzie doskonałe, ale bez wątpienia, w tej chwili jest to wiodący ORM dla PHP.

Jedną z wad Propela, która pojawia się chyba najczęściej jest XML i definiowanie tabel w pliku XML. Otóż drodzy moi, nie jest to konieczność. Schemat z istniejącej bazy danych można bez problemu przenieść do XMLa a następnie bez najmniejszego problemu wygenerować z niego klasy. Możemy zrobić to dwoma poleceniami. Pierwsze jest dostępne po instalacji przy pomocy PEARa, drugie przy korzystaniu z Phinga:

```
// propel-gen project target
propel-gen sheep creole
propel-gen sheep om
// bądź phing -Dproject=project -Dtarget=target
phing -Dproject=sheep -Dtarget=creole
phing -Dproject=sheep -Dtarget=om
```

Naszym oczom ukaże się kilka informacji o przebiegu całego procesu, po czym będziemy mogli korzystać z wygenerowanych klas. :)

**Propel 1.3**
Propel 1.3 korzysta z PDO dlatego też, task "creole" niestety nie działa. Jeśli ktoś chce korzystać z możliwości generowania schematu musi niestety korzystać na przemian z wersji 1.2 i 1.3 (1.2 generuje schemat, 1.3 generuje klasy).

**Migrowanie pomiędzy bazami danych**
Propel umożliwia zrzucenie struktury bazy danych do pliku zawierającego definicje tabel. Aby to zrobić wystarczy skorzystać z taska _sql_. Warto pamiętać, że schemat SQL jest budowany na podstawie pliku XML. Zmieniając jedną dyrektywę w konfiguracji generatora (propel.database.url) można bez problemu przenieść całą bazę

**Przenoszenie danych**
Istnieje również możliwość przeniesienia danych z istniejącej bazy, służy do tego task datadump. Propel zrzuca zawartość wszystkich tabeli do plików XML po to by móc następnie wygenerować z niego zapytania insert. Aby to zrobić wystarczą dwa polecenia:
```
propel-gen sheep datadump
propel-gen sheep datasql
```

**Propel a ociężałość**
Trudno się nie zgodzić, że używanie narzędzi typu ORM powoduje wydłużenie czasu wykonywania skryptu, ale jak by nie patrzeć, znacznie też skraca czas potrzebny na tworzenie kodu odpowiedzialnego za warstwę DAO, co zwykle tuż po tworzeniu widoku zajmuje najwięcej czasu.
Myślę, że warto tu wspomnieć o Propelu 2.0, który ma obsługiwać dostęp do pól przez metody \_\_get i \_\_set dzięki czemu całość może stać się lżejsza a ilość kodu, który jest generowanego ulegnie widocznemu zmniejszeniu.

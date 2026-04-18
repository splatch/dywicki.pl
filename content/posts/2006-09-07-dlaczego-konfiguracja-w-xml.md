---
author: splatch
category:
  - php
  - wiadomości
  - xml
date: "2006-09-07T20:39:15+00:00"
guid: http://blog.dywicki.pl/2006/09/07/dlaczego-konfiguracja-w-xml/
title: Dlaczego konfiguracja w XML.
url: /2006/09/07/dlaczego-konfiguracja-w-xml/

---
W czasach kiedy najbardziej trendy jest YAML twierdzę, że jest on niczym w porównaniu do tego, co oferuje XML. DLaczego?

Dlatego, że tworząc dowolny dokument XML mogę go w bardzo prosty sposób rozszeżyć. Jak? Poprzez XInclude. Jego obsługa jest nawet w PHP więc nie ma z tym jakichkolwiek problemów. Definiuję tylko odnośnik  i mam dołączony ten XML \[po wykonaniu $DOMDocument->xinclude();\].

Kolejna sprawa. Walidacja. Nie muszę tworzyć żadnego kodu w PHP by sprawdzić poprawność XMLa. Wystarczy, że stworzę dobry schemat w XSD i mam walidację załatwioną bez jakiegokolwiek warunku. Do tego dochodzą ograniczenia takie jak rekurencja. W PHP  muszę to załatwiać poprzez wielokrotne wywołanie funkcji, które za każdym razem wydłuża czas. Normalnie załatwi mi to DOM XML, który jest napisany w C i będzie znacznie szybszy.

Parę linijek kodu i moja konfiguracja zapisana w XMLu stanie się czytelna dla oka przy użyciu XSLT w połączeniu z PHP.
Także czy dalej YAML i INI jest najlepszym sposobem przechowywania konfiguracji? Moim zdaniem nie - i nigdy nie będzie. XML jest technologią, która fakt faktem, jest nadmiarowa, ale oferuje bardzo dużą popularność. Można mówić, że YAML jest czytelny i przyjazny, ale nie oferuje takiej funkcjonalności przy takim małym nakładzie sił. XML jest wszędzie, jest prosty. Sprawdzanie poprawności dokumentu można załatwić poprzez inny XML a jego prezentacje poprzez kolejny, także znajomość technologii związanych z XMLem jest niewątpliwie przydatna.

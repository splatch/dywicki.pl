---
author: splatch
category:
  - inne
date: "2005-12-23T14:57:38+00:00"
guid: http://blog2.dywicki.pl/2005/12/23/repozytorium-svn/
title: Repozytorium SVN
url: /2005/12/23/repozytorium-svn/

---
Framework, który usiłuję pisać można pobrać z **svn://ivpro.net/PSF**. Kod jest przepisywany na nowo, ponieważ zmieniła się nieco koncepcja :).
Lista zmian:
\- Przesunięcie tablicy z mapą ConfigManager::$map do oddzielnej klasy ConfigMapper (dziedziczącej klasy abstrakcyjnej Mapper, pakiet Mapper) opartej na singletonie.
\- Dodanie nowej klasy konkretnej - ConfigProperties, która parsuje config przy pomocy **parse\_ini\_file**. Zastosowanie w sam raz do tworzenia plików językowych. Z tego rodzaju configu nie można uzyskać struktury takiej jak w pliku XML.
\- Przesunięcie funkcji do obsługi tablic do klasy ArrayUtil w pakiecie Util.
\- Zlikwidowanie pakietu AutoLoad i przeniesienie go częściowo do pakietu Util (część AutoLoad powędrowała do pl.splatch.BuildTool jako task Phinga).
\- Nowy pakiet - Dispatcher (jak narazie bez wyraźnego podziału na klasy do obsługi widoku, requestu) służący do wykonywania forwardów jak i redirectów.
\- Dodanie "warstwy usług" - nowy pakiet - Service (jeśli zajdzie potrzeba powstanie również Service.Activator).
\- Wprowadzenie większej liczby interfejsów w miejsce dziedziczenia z klas abstrakcyjnych.
I jeszcze pare innych kosmetycznych poprawek...

Przy okazji - wesołych świąt, wolnych od pracy, w ciepłej, rodzinnej i przyjaznej atmosferze :)

---
author: splatch
category:
  - thor
date: "2005-06-19T10:19:53+00:00"
guid: http://blog2.dywicki.pl/2005/06/19/thor/
title: Thor - postępy
url: /2005/06/19/thor/

---
Prace nad Thorem posunęły się do przodu. Mam teraz podstawowe klasy – TComponent, TParser (parser komponentów), ConfigParser i fabrykę do niego. Przy okazji chciałbym się pochwalić rozwiązaniem, jakie wymyśliłem.  

Podobnie jak w Prado każdy komponent to klasa, która ma określone metody i atrybuty. W We wcześniej wymienionym frameworku w pliku konfiguracyjnym komponentu definiuje się metody do pobierania/ustawiania atrybutów poprzez wpisanie w pliku specyfikacji danego komponentu. U mnie wygląda to nieco inaczej – klasa TComponent, z której dziedziczą wszystkie pozostałe komponenty ma zaaplikowaną metodę \_\_call(), która umożliwia tworzenie ‘wirtualnych metod’. Tzn. metod, które nie istnieją w klasie macierzystej.  

Może przedstawię fragment pliku konfiguracyjnego:  

```
 [properts] Action = text Method = text DefaultLogin = text DefaultPassword = text [events] OnClick OnSubmit [Action] get = true set = true [Method] get = true
```

Teraz, chyba będzie łatwiej zrozumieć ;), wszystkie sekcje, które rozpoczynają się od wielkiej litery traktowane są jak atrybuty komponentu. Sekcja Method odnosi się do atrybutu Komponent::$Method. Teraz, gdy w sekcji Metod jest ustawiona dyrektywa **get** na true będzie można odwoływać się w kodzie do tego atrybutu poprzez getMethod w szablonie.  

Wiem, że to troszkę niejasne, ale kiedy uda mi się stworzyć jakiś działający przykład to nie omieszkam pokazać. Przykładowy kod szablonu to:

```
 <thor:Komponent ID=”C1” params="true">Tutaj jakoś kod</thor:komponent> tutaj kod html <thor:Inny ID="C2" onclick="doSth();" />
```

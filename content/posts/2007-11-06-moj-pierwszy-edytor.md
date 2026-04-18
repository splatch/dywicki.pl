---
author: splatch
category:
  - agavi
  - eclipse
  - java
  - jaxb
  - php
  - swt
  - wiadomości
  - xml
date: "2007-11-06T22:44:39+00:00"
guid: http://blog.dywicki.pl/?p=196
title: Mój pierwszy edytor...
url: /2007/11/07/moj-pierwszy-edytor/

---
[![Agavi module configuration editor](/wp-content/uploads/2007/11/agavi-editor.thumbnail.JPG)](/wp-content/uploads/2007/11/agavi-editor.JPG "Agavi module configuration editor")

Czas jakiś temu udało mi się stworzyć pierwszy działający edytor w oparciu o Eclipse.
Kilka informacji co pod spodem:

- SharedHeaderFormEditor - ładny tekstowy nagłówek oraz możliwość dodawania zakładek
- FormPage i FormToolkit - użyte do stworzenia układu, który widać na załączonym obrazku
- JFace Databinding - zapewnia propagowanie zmian z formularza do obiektów
- JAXB 2 - mapowanie plików XML do odpowiednich klas
- JFace - table viewer i parę innych dodatków

Całość ogólnie jest prosta niczym konstrukcja cepa, w działaniu sprawia się dobrze. Jak tylko ogarnę kod postaram się opisać krok po kroku jak można coś podobnego stworzyć na własny użytek.

Splatch wraca do gry i będzie grać wysoko! ;-)

---
author: splatch
category:
  - mvc
  - php
date: "2006-02-14T01:26:54+00:00"
guid: http://blog2.dywicki.pl/2006/02/14/proste-testy/
title: 'Proste testy:'
url: /2006/02/14/proste-testy/

---
Rozmawiałem wczoraj z Zyxem na temat testów czasowych i dowiedziałem się, że korzysta on do tego celu z apache benchmark - nie zwlekając również wykonałem testy...

Pomiary wykonane przy użyciu apache benchmark (apache 2.0.53, php 5.1.1) dla 500 requestów.
Odczyt pliku 70 KB
file+implode: 5.640589 s
file\_get\_contents: 2.562484 s

Frameworki:
CakePHP (z warrningiem dot. połączenia mysql): 76.421385 s
Mojavi 3.0-dev (debug\_mode = off): 63.155846 s
Agavi 0.10-stable (debug\_mode = off): 51.187172 s
PSF: 46.390328 s (bez cache configa etc)
Prado 2.0.1 (też Hangman): 169.14543 s
Prado 3.0-alpha (Samples.Hangman.Home): 196.279994 s
Odin: 28.499818 s
Phiend: 11.359303 s (gołe hello world)
Daeron: 1.156242 s
Pinky: 53.843750 s

Wygrywa, jak zwykle pod względem optymalizacji niedościgniony Odin.. phienda nie biorę pod uwagę ponieważ w wersji którą mam u siebie nie jest to pełna implementacja MVC - kod html w kodzie akcji, Daeron kończył pracę bardzo wcześnie, jak tylko wykrył, że nie mam zainstalowanego PDO. Co mogę powiedzieć o Prado - optymalizacja jego developerom jest najwyraźniej obca.. miło mnie zaskoczył lepszy wynik agavi od mojavi, widać coś poprawili :). Cieszy mnie również, że PSF jest na 2 miejscu, a jeśli się je doszlifuje, może przybliżyć się do pierwszego miejsca.. :)

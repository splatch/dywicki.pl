---
author: splatch
category:
  - apache
date: "2006-02-21T13:26:56+00:00"
guid: http://blog2.dywicki.pl/2006/02/21/mod_rewrite-plus-zmienne-z-get/
title: mod_rewrite plus zmienne z GET
url: /2006/02/21/mod_rewrite-plus-zmienne-z-get/

---
Dzisiaj miałem nietypowy problem - chciałem stworzyć sobie obsługę mod\_rewrite ale nie babrać się z wyciąganiem zmiennych które był w żądanym adresie, dlatego też udałem się do google.pl w poszukiwaniu dodatkowych flag, które można ustawiać. Znalazłem bardzo fajną stronkę z kartą podręczną do mod\_rewrite - http://www.ilovejackdaniels.com/cheat-sheets/mod\_rewrite-cheat-sheet/ jest ich tam więcej - css, php, javascript, html, mysql. A moje rozwiązanie wygląda w następujący sposób:
`RewriteEngine  On`

RewriteBase /z-tego-adresu/
RewriteCond %{REQUEST\_URI} !^.\*\\.(css\|png\|gif\|jpe?g\|php\|js)$
RewriteRule ^.\*$ /z-tego-adresu/index.php?%{REQUEST\_URI} \[QSA,L\]
Oczywiście wymaga dotarcia i obsługi w kodzie, ale GET jest bez zmian.. i to najważniejsze :)

---
author: splatch
category:
  - php
date: "2006-02-27T00:56:08+00:00"
guid: http://blog2.dywicki.pl/2006/02/27/tworzenie-nowej-instancji-klasy/
title: Tworzenie nowej instancji klasy...
url: /2006/02/27/tworzenie-nowej-instancji-klasy/

---
Nie wiem jak Wy, ale ja kilka razy spotkałem się z sytuacją, że chciałem przekazać do konstuktora kilka argumentów, ale niestety urocze call\_user\_func\_array nie daje takiej możliwości. Dlatego też w PSF musiałem wynaleść takie coś jak interfejs LazyService by móc tworzyć usługi doiero w chwili ich wywołania. Sam sposób nie jest najleszy.  

 Teraz, po niewczasie znalazłem sosób na to, sposób bardzo prosty, który nie wymusza użycie eval - swoją drogą kolejny dowód na to, że bez eval da się żyć :) - oto on:  
` $r = new ReflectionClass('ClassName');
 $r->newInstance(1,2,3); `  

 Do konstruktora zostaną przekazane trzy argumenty - 1, 2 oraz 3. Konstrukcje eval do tworzenia nowych bytów wdziałem w implementacji IoC dla PHP, mianowicie w PicoContainer. Swoją drogą dziwne, ponieważ biblioteka sporo korzysta z Reflection API.. czyżby pora na zmiany?.. w kodzie? :)

---
author: splatch
category:
  - wiadomości
date: "2006-09-07T20:08:41+00:00"
guid: http://blog.dywicki.pl/2006/09/07/ajax-i-wiele-domen/
title: Ajax i wiele domen
url: /2006/09/07/ajax-i-wiele-domen/

---
Jakiś czas temu w pracy dostałem troszkę inne zadanie. Mianowicie, poprawić konfigurację Apache. Konfiguracja jak konfiguracja, to nie był problem - schody zaczęły się z dodaniem virtual hostów. Zawsze miałem z tym problem, teraz do tego dochodziło skonfigurowanie tego wszystkiego z użyciem SSLa. Jak już sobie z tym poradziłem - doszło kolejne zadanie, czyli konfiguracja proxy! Problem polegał na tym, że nasza kontrolka webowa komunikuje się z serwerem, z tym, że serwer może stać na dowolnej maszynie.
Przyznam, że po tym co przeszedłem z tym SSLem i vhostami miałem serdecznie dość wszystkiego co było z httpd.conf związane. ;)

W ramach pomocy otrzymałem od project managera link z opisem konfiguracji jakiegoś proxy. No to do dzieła, gotowiec - na dole.

```
ProxyRequests on
# ProxyPass "lokalny folder" "serwer zewnetrzny"
ProxyPass /delta/ https://delta.dywicki.pl/
# każde żądanie do tego folderu
ProxyPassReverse /
```

W ten sposób, wszystkie żądania, które trafiają do /delta/ idą na delta.dywicki.pl. Rzecz mała i prosta. Jedyny minus tego rozwiązania to konieczność konfiguracji proxy i włączenie modułów które domyślnie są wyłączone.

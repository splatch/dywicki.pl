---
author: splatch
category:
  - wiadomości
date: "2008-10-05T21:20:05+00:00"
draft: "true"
guid: http://blog.dywicki.pl/?p=228
title: Maven jako narzędzie wspomagające pracę w dużych organizacjach
url: /

---
Maven jest narzędziem złożonym. Tego oczywiście nie da się zaprzeczyć. Sami twórcy mówią o "filozofii Mavena", jakkolwiek można to określić raczej jako marketing. Maven po prostu stara się wprowadzić porządek i jakiś standard tworzenia projektów. Niestety jest to rozwiązanie związane z wyłącznie z Javą, programiści PHP. Dla .NET istnieje [NMaven](http://incubator.apache.org/nmaven/) (czyżby wszystko co dobre w Javie ma kopię w .NET? :))

# Możliwości

Maven dostarcza szeregu gotowych rozszerzeń, które załatwiają większość pracy za nas. Nie musimy na przykład nic określać lokalizacji plików .jsp i innych tym podobnych jeśli budujemy aplikację webową. Wiadomo, że w przypadku większych organizacji, które prowadziły wiele projektów przez dłuższy czas, istniały po prostu stosy plików [Ant](http://ant.apache.org), które symulowały tą samą funkcjonalność. Teraz nie musisz się tym troszczyć.

# Ograniczenia

Jak każde narzędzie, również i Maven ma ograniczenia. Nie wszystko się da, nie do wszystkiego jest wtyczka. Ale to, czego się nie da zrobić automatycznie można obsłużyć skryptem bądź wstawką Ant. Z resztą, jak na którymś z forów dyskusyjnych autorzy [Maven](http://www.sonatype.com/community/definitive_guide.html)

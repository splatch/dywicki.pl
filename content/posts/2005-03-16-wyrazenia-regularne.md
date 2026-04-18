---
author: splatch
category:
  - php
date: "2005-03-16T14:30:27+00:00"
guid: http://blog2.dywicki.pl/2005/03/16/first-post/
title: Wyrażenia regularne
url: /2005/03/16/first-post/

---
Od niechcenia spojrzałem dzisiaj na forum. I co widzę? Temat **(php) Problem - regexy**. Jako, że mam jako-takie pojęcie na temat regexpów to zobaczyłem o co chodzi. Okazało się, że to nic wielkiego, problem ze zmianą \[\[link\]\] na wersję zdatną do klikania. Jedynym problemem było to, że drugi parametr jest opcjonalny. Zacząłem myśleć, już po chwili miałem gotowy wzór, który pasował do \[\[link\]\] oraz \[\[link\|opis\]\]. Zastanawiało mnie jedno - czy da się wykorzystać w **preg_replace**; ten wzór.. Niestety nie udało mi się do tego dojść mimo lektury bardzo dobrego [poradnika](http://thebat.pl/podrecznik/TB_regx.php) na temat wyrażeń. Udało mi się w końcu zrodzić drugi wzór, znacznie bardziej skomplikowany, na zasadzie [pod-wyrażeń warunkowych](http://thebat.pl/podrecznik/TB_regx_advsy_cnd.php).

```php
<?php
// tekst do przerobki $text = '[[link|tata]]';
// wersja z podwyrazeniami (niezaimpletowana w funkcji)
$regexp = '/(?(?=[[(?:.+)|(?:.+)]])[[(.+)|(.+)]]|[[(.+)]])/';

// wersja alternatywna;)
$regexp = '/[[(.+?)(?:|(.+))?]]/';
$tex2 = preg_replace_callback($regexp,'wiki_link',$text);

function wiki_link($mat) {
     if (sizeof($mat) == 3) {
         // jest drugi parametr - [link|opis]
         return '<a href="'. $mat[1] .'" title="'. $mat[0] .'">'. $mat[2] .'</a>';
     }  
     return '<a href="'. $mat[1] .'" title="'. $mat[0] .'">'. $mat[1] .'</a>';
}
?>
```

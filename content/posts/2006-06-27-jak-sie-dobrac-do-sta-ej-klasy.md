---
author: splatch
category:
  - php
date: "2006-06-27T22:20:05+00:00"
guid: http://blog.dywicki.pl/2006/06/28/jak-sie-dobrac-do-stalej-klasy/
title: Jak się dobrać do stałej klasy
url: /2006/06/28/jak-sie-dobrac-do-stalej-klasy/

---
Mały konkursik - jak pobrać stałą z klasy..? Kod jest, jedyny problem to to, że nazwa stałej może się zmienić...
\[php\]< ?php
class Foo {
const TAK = 'prawda';
const NIE = 'fałsz';
const CHGW = 'a kto go tam wie';
}

if($\_GET\['imie'\] == 'Bolek') {
// $constant = 'TAK';
} elseif(!empty($\_GET)) {
// $constant = 'NIE';
} else {
// $constant = 'CHGW';
}
echo '... wlasnie co ? ..';
?>\[/php\]

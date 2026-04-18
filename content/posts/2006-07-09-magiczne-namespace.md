---
author: splatch
category:
  - php
  - php6
  - wiadomości
date: "2006-07-09T13:41:02+00:00"
guid: http://blog.dywicki.pl/2006/07/09/magiczne-namespace/
title: Magiczne namespace..
url: /2006/07/09/magiczne-namespace/

---
I oto się stało. Pierwszy raz użyłem przestrzeni nazw w PHP! Nie do wiary? A jednak. Nie było jakichkolwiek problemów z samą instalacją, ponieważ do pobrania jest paczka (pod Win ;)), która zachowuje się jak wszystkie inne pobrane z [php.net](http://php.net). Przykłady podane na necie działają, więc nie pozostaje nic innego jak zabrać się za używanie przestrzeni nazw. :)

Oto listingi, które działają:
\[php\]< ?php

import class a:::AFactory;
import class a:::A;

namespace a {
class AFactory {
public static function create() {
return new A;
}
}
private class A {}
}

// works perfectly:
AFactory::create();

// fails with 'Fatal error: Cannot use class 'a:::A' outside of its namespace, as it is private'
// new A;

?>\[/php\]

Oraz:
\[php\]< ?php

import namespace a;

function \_\_autoload($classname) {
foreach (get\_imported\_namespaces() as $ns) {
$filename = str\_replace( ':::', '/', $ns ) . '/' . $classname . '.php';
if (file\_exists($filename)) {
require\_once($filename); // will require a/A.php
autoload\_import\_class($classname, $ns); // will import class a:::A
}
}
}

new A;

?>
// file a/A.php:
< ?php

namespace a {
class A {}
}

?>\[/php\]

## [Więcej informacji http://phpnamespaces.org/](http://phpnamespaces.org/)

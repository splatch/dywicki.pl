---
author: splatch
category:
  - php
date: "2005-12-01T10:45:20+00:00"
guid: http://blog2.dywicki.pl/2005/12/01/system-szablonow/
title: Parser szablonów
url: /2005/12/01/system-szablonow/

---
Wczoraj, bądź przedwczoraj wpadłem na pomysł wykorzystania DOM XML i XSL przy tworzeniu szablonów. Zainspirował mnie PHP TAL (Template Attribute Language).

Z początku szablon miał być stylem XSL, jednak pomysł ten szybko odpadł ze względu na to, że uniemożliwia to tworzenie własnych komponentów, a przynajmniej ja nie wiem jak to zrobić. W tym układzie w pamięci przechowywany byłby obiekt DomDocument z odpowiednią struktórą - odwzorowaniem dodanych zmiennych.
Drugi pomysł wyklucza użycie XSL. Szablon jest parsowany DOM XMLem a następnie rekurencyjnie przeglądany. Na podstawie nazw tagów i zarejestrowanych na początku szablonu rozszeżeń parser tworzy odpowiednie obiekty odpowiadające za komponenty. Ogólnie idea tworzenia własnych rozszeżeń jest rodem z JSP.
Nie wiem co z tego wyjdzie i wogóle czy coś z tego będzie.. ;)

Jedno jest pewne - odpada problem z gromadą wyrażeń regularnych, pozbywam się kompilatora. Zostaje parser i być może cache. Sam jestem ciekaw czy to rozwiązanie będzie szybsze niż np. Smarty.
Poniżej bardzo abstrakcyjne przykłady.

```xml
<?xml version="1.0" ?>
<x xmlns:i18n="http://template.splatch.pl/i18n"
    xmlns:pst="http://template.splatch.pl/pst"
    xmlns:attr="http://template.splatch.pl/attr"
    xmlns="http://template.splatch.pl/" >
    <i18n:message select="Hello">
        <pst:test equals="true" assigned:variable="AssignedVar" assigned:value="OtherVar">
            Test 1
        </pst:test>
        <pst:test identical="true" assigned:variable="AssignedVar" value="TestVar">
            Test 2
        </pst:test>
    </i18n:message>
</x>
```

```php
<?php

$tpl = new PST;
$i18 = ConfigParserFactory::get('i18n-test/pl.ini');
$tpl->setI18nDatasource($i18); // ConfigParser

$tpl->assign('AssignedVar',true);
$tpl->assign('OtherVar',false);

$tpl->display('test.xml');

?>
```

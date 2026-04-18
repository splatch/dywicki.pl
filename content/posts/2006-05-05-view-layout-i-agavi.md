---
author: splatch
category:
  - agavi
  - framework
  - mvc
  - php
date: "2006-05-05T11:17:54+00:00"
guid: http://blog.dywicki.pl/2006/05/05/view-layout-i-agavi/
summary: Zainspirowany ideą Layotów w M4 postanowiłem stworzyć małą wtyczkę do agavi, która umożliwi mi korzystanie z tego rozwiązania. Jest ona bardzo prosta i ułatwia masową zmianę szablonu głównego oraz slotów (o slotach jeszcze będę pisał).
title: View Layout i Agavi
url: /2006/05/05/view-layout-i-agavi/

---
Zainspirowany ideą Layotów w M4 postanowiłem stworzyć małą wtyczkę do agavi, która umożliwi mi korzystanie z tego rozwiązania. Jest ona bardzo prosta i ułatwia masową zmianę szablonu głównego oraz slotów (o slotach jeszcze będę pisał).

Oto moje rozwiązanie:
```php
<?php

class ViewLayout {

static public function set($layout, View $view) {
$config = self::getConfig($layout);

if(isset($config['decorator']['directory'])) {
$view->setDecoratorDirectory($config['decorator']['directory']);
$view->setDecoratorTemplate($config['decorator']['template']);
}
if($config['database'] == true) {
Propel::init(PropelDatabase::getDefaultConfigPath());
}
if(isset($config['slots']) && sizeof($config['slots']) > 0) {
try {
$view->setSlots($config['slots']);
} catch(AgaviException $e) {
$view->clearSlots();
}
}
}

static protected function getConfig($layout) {
$filename = realpath(dirname(__FILE__)) .'/'. $layout .'.layout.ini';
if($filename = ConfigCache::checkConfig($filename)) {
return include($filename);
}
return array();
}
}
?>
```

Instalacja:
Stworzyć w katalogu lib katalog layout a w nim plik ViewLayout.class.php. Jego zawartość jest wyżej.
1\. Dodać w autoload.ini:
```
ViewLayout = "%AG_LIB_DIR%/layout/ViewLayout.class.php"
```

2\. Dodac w config\_handlers.ini linijki:
```
[*layout.ini]
class = ReturnArrayConfigHandler
```

3\. Stworzyć plik konfiguracyjny (np. frontend.layout.ini) w tym samym katalogu co plik z klasą. Przykładowy plik:
```ini
;ścieżki dla dekoratora (opcjonalnie)
decorator.template = frontend.tpl
decorator.directory = %AG_TEMPLATE_DIR%

; czy inicjować sesję propela (opcjonalnie)
database = true

; uwaga, składnia zgodna z metodą setSlots
; klasy View, poprawna notacja
; slots..module_name =
; slots..action_name =
slots.slot_last_article.module_name = Default
slots.slot_last_article.action_name = LastArticle

slots.slot_last_articles.module_name = Default
slots.slot_last_articles.action_name = LastArticles
```

4\. Użycie w klasie widoku:
```php
<?php
ViewLayout::set('frontend', $this);
?>
```
Myślę, że mechanizm jest na tyle prosty, że można go rozszeżać do własnych potrzeb. Dla każdego layoutu powinien istnieć plik konfiguracyjny /lib/layout/.layout.ini. W następnej części postaram się opisać filtr umożliwiający request-mapping.

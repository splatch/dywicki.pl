---
author: splatch
category:
  - java
  - jaxb
  - php
  - xml
date: "2007-11-08T23:52:05+00:00"
guid: http://blog.dywicki.pl/?p=197
summary: |-
  Integracja między językami czy też platformami to kwestia poruszana nie od dzisiaj. Na poziomie platform funkcjonuje od dłuższego czasu CORBA i Web Services z trio SOAP + WSDL + XML Schema na czele. Integracja systemów napisanych w tym samym języku sprowadza się zwykle do wykorzystania serializacji, która jest najszybsza i najwygodniejsza. Gorzej jeśli idzie o połączenie dwóch języków - w moim przypadku PHP i Javy.
  Zend ma swój mostek, który umożliwia na zintegrowanie Javy i PHP, jest też dodatkowe rozszerzenie do PHP, które pozwala na wykorzystanie Javy w PHP, jednakże moje oczekiwania nie był aż tak wielkie. Potrzebowałem po prostu odczytać dane specyficzne dla PHP - powiedzmy informacje o jakiejś klasie. Standardowo taka operacja wymagała stworzenia parsera, co jest zadaniem powiedzmy, nie na moje siły i umiejętności.. stąd też postanowiłem sobie nieco uprościć pracę. :)
title: Weź do Javy to co chcesz z PHP!
url: /2007/11/09/wez-do-javy-to-co-chcesz-z-php/

---
Integracja między językami czy też platformami to kwestia poruszana nie od dzisiaj. Na poziomie platform funkcjonuje od dłuższego czasu CORBA i Web Services z trio SOAP + WSDL + XML Schema na czele. Integracja systemów napisanych w tym samym języku sprowadza się zwykle do wykorzystania serializacji, która jest najszybsza i najwygodniejsza. Gorzej jeśli idzie o połączenie dwóch języków - w moim przypadku PHP i Javy.
Zend ma swój mostek, który umożliwia na zintegrowanie Javy i PHP, jest też dodatkowe rozszerzenie do PHP, które pozwala na wykorzystanie Javy w PHP, jednakże moje oczekiwania nie był aż tak wielkie. Potrzebowałem po prostu odczytać dane specyficzne dla PHP - powiedzmy informacje o jakiejś klasie. Standardowo taka operacja wymagała stworzenia parsera, co jest zadaniem powiedzmy, nie na moje siły i umiejętności.. stąd też postanowiłem sobie nieco uprościć pracę. :)

Wspólny, najwygodniejszy format (zarówno w odczycie i zapisie danych) z jednej i drugiej strony to oczywiście XML. Problem w tym, jaki format ma być wykorzystany. Nie da się przecież bezpośrednio odwzorować obiektu z PHP do Javy głównie z racji na dynamikę. Jeśli w PHP ktoś dorzuci pole do obiektu, poprzez proste **$someUser->city = 'Białystok'** to Java bazująca tylko na statycznych, zadeklarowanych polach w klasie nie odczyta tej informacji. Serializacja w postaci specyficznej dla PHP również wiąże się ze stworzeniem parsera po stronie Javy by to wszystko obsługiwać i dodatkowo coś co by później mapowało obiekty z Javy do XMLa w postaci przyjaznej dla PHP. Wyjściem z całej sytuacji okazały się funkcje [wddx\_\*](http://pl2.php.net/manual/en/ref.wddx.php). Po prostu strzał w dziesiątkę. [WDDX](http://www.openwddx.org/) to standard może nie najnowszy, ale dosyć spójny, i co najważniejsze umożliwiający przesyłanie złożonych obiektów bez zbytniej walki. Po chwili poszukiwań znalazłem [DTD](http://www.openwddx.org/downloads/dtd/wddx_dtd_10.txt), zatem ze strony Javy wystarczy odpalić [JAXB](http://java.sun.com/webservices/jaxb/index.jsp) i jesteśmy na miejscu.

Przykładowy skrypt PHP, który uzyskuje informacje o konfiguracji Agavi:
```php
<?php
include_once 'E:/htdocs/shop/agavi/agavi.php';
$value = wddx_serialize_value(AgaviConfig::export());

$value = "<?xml version='1.0' encoding='utf-8' ?>\n<!DOCTYPE wddxPacket SYSTEM 'wddx.dtd'>\n" . $value;

echo $value;
```

A teraz część wyniku, który PHP wyświetla w konsoli:
```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE wddxPacket SYSTEM 'wddx.dtd'>
<wddxpacket version='1.0'>
 <header />
 <data>
 <struct>
 <var name='core.minimum_php_version'>
 <string>5.1.0</string>
 </var>
 <var name='core.agavi_dir'>
 <string>E:\htdocs\shop\agavi</string>
 </var>
 <!-- i tak dalej -->
 </struct>
 </data>
</wddxpacket>
```

Teraz kod Javy, który odczytuje sobie informacje.. (nawiasy kwadratowe przy listach podyktowane błędami w skrypcie, który koloruje składnię)
```java
 // odpalamy interpreter PHP
 Runtime runtime = Runtime.getRuntime();
 Process exec = runtime.exec("php -q E:/agavi-ide/org.codehouse.bridge/src/org/codehouse/bridge/test2.php");

 // podnosimy kontekst JAXB
 JAXBContext context = JAXBContext.newInstance(ObjectFactory.class);
 // deserializujemy XML wygenerowany przez PHP
 WddxPacket object = (WddxPacket) context.createUnmarshaller().unmarshal(exec.getInputStream());

 // odczytujemy informacje
 for (Object stc : object.getData().getWDDXData()) {
 // spodziewamy się informacji o typie złożonym
 if (stc instanceof Struct) {
 List<generated.Var> vara = ((Struct) stc).getVar();
 for (Var value : vara) {
 // pozostaje nam tylko odczytanie zserializowanej wartości
 List<Object> configurationValue = value.getWDDXData();
 System.out.println(value.getName() + ": " + ((generated.String) configurationValue.get(0)).getvalue());
 }
 }
 }
```

Wynik działania poniższego kodu to:
```
core.minimum_php_version: 5.1.0
core.agavi_dir: E:\htdocs\shop\agavi
exception.default_template: E:\htdocs\shop\agavi/exception/templates/shiny.php
agavi.name: Agavi
agavi.major_version: 0
agavi.minor_version: 11
agavi.micro_version: 0
agavi.status: DEV
agavi.branch: trunk
agavi.version: 0.11.0-DEV
agavi.release: Agavi 0.11.0-DEV
agavi.url: http://www.agavi.org
agavi_info: Agavi 0.11.0-DEV (http://www.agavi.org)
```

Czyli wszystko czego trzeba było nam do szczęścia! :)

Pora na przykład bardziej złożony, wyciągnięcie informacji o jakiejś klasie widoku użytej w aplikacji opartej o Agavi.
```php
<?php

include_once 'E:/htdocs/shop/agavi/agavi.php';

AgaviConfig::set('core.app_dir', 'E:/htdocs/shop/project/');
AgaviConfig::set('core.system_config_dir', 'E:/htdocs/shop/agavi/config/defaults/');

// metoda dopisana przeze mnie, konfiguruje Agavi bez wyrzucania wyjątków.
Agavi::bootstrap2('');

include_once 'E:/htdocs/shop/project/modules/Cart/lib/BaseInputView.class.php';

$obj = new ReflectionClass('Cart_BaseInputView');
$orig = $obj->getFileName();

$myMethods = $obj->getMethods();

$methods = array();

foreach ($myMethods as $method) {
 // sprawdzenie czy plik w którym jest zadeklarowana metoda
 // pokrywa się z plikiem w którym jest zadeklarowana klasa
 // w ten sposób pozbywam się niepotrzebnych metod z klas nadrzędnych
 if ($method->getFileName() == $orig) {
 $methods[] = $method;
 }
}

$value = wddx_serialize_value($methods);
$value = "<?xml version='1.0' encoding='utf-8' ?><!DOCTYPE wddxPacket SYSTEM 'wddx.dtd'>\n" . $value;
echo $value;

?>
```

Drobna przeróbka kodu Javy, który był użyty wcześniej i wynikiem jest
```
php_class_name: ReflectionMethod
name: execute
class: Cart_BaseInputView
```

Tym, którzy dobrnęli do końca tej noty dziękuję za wytrwałość. :) Kolejna nota będzie być może o tym jak wywoływać metody statyczne klas Javy z poziomu XSLT (wierzcie mi, da się!).

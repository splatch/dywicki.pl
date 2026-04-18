---
author: splatch
category:
  - framework
  - ogólne
  - php
date: "2006-10-03T18:54:39+00:00"
guid: http://blog.dywicki.pl/2006/10/03/zend-framework-i-inni/
title: Zend Framework i inni
url: /2006/10/03/zend-framework-and-others/

---
Zend od jakiegoś czasu rozwija z powodzeniem swój framework. Szturmuje on rynek dzięki wsparciu firmy i dobrej dokumentacji. Zastanawia mnie jednak, dlaczego inni zaczęli kopiować to co w ZF jest. Rozumiem konwencję nazewniczą, ok - to może komuś się podobać, rozumiem strukturę katalogów, może ktoś uzna ją za logiczną.. Nie mniej nazewnictwo i struktura prawdę mówiąc nie różni się niczym od tego co było standardem w PEAR.

Co więcej, niektórzy po prostu przepisują spore fragmenty kodu, które są w ZF na swoje. Zapytam po co? Skoro jest coś podobnego w Zendzie to jaki sens jest w powielaniu praktycznie tego samego (Zend::loadClass, ZendRegistry, Zend\_Router\_Rewrite itp.)? Pomijam fakt, że [Zend jest otwarty w tej chwili i na pomysły i na ludzi](http://framework.zend.com/wiki/display/ZFDEV/Home) i zapytam, czy to ma jakiś sens?

Nie. Nie ma najmniejszego sensu ponieważ klony padną. ZF na 90% kiedyś wyjdzie i z powodzeniem wyprze wszystkie klony. Wyjście ZF to w dużej mierze kwestia prestiżu i marketingu a także być albo nie być dla PHP w świecie "rapid application development", zgodnie z tym co głosi oficjalna strona [http://framework.zend.com](http://framework.zend.com):
_Now, the world's most popular web programming language gets even better with an easy to use framework for developing the next generation of web applications._.
Bez tego Ruby oraz inne języki wspierane frameworkami po prostu zepchną PHP w kąt. Jak widać po statystykach PHP odrobiło spore straty rosnące niemal bez przerwy od zeszłego roku. Czy to tylko zasługa ZF? Wydaje mi się, że w dużej mierze tak.

[![Statystyki php 2006-09](http://img.dywicki.pl/phpstats.png)](http://pl2.php.net/usage.php)
Wnioskuję, że jeśli ktoś już zaczął robić framework wzorowany na zendowym to zna architekturę tego drugiego i nie tylko łatwiej ale i szybciej byłoby stworzyć to w oparciu o ZF.
Pragnę nadmienić, że ZF ewoluuje, ostatnie propozycje zmian dotyczą między innymi warstwy MVC (dodatkowe informacje [tu](http://framework.zend.com/wiki/display/ZFPROP/Controller+Subdirectories+Proposal+-+Jeroen+Keppens) i [tu](http://framework.zend.com/wiki/display/ZFPROP/MVC+Reorganization+Proposal+-+Matthew+Weier+O%27Phinney)).

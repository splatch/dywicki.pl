---
author: splatch
category:
  - javascript
date: "2005-06-08T18:29:10+00:00"
guid: http://blog2.dywicki.pl/2005/06/08/wykrywanie-przegldarki/
summary: |-
  Diziaj znajomy grafik poprosił mnie o stworzenie skryptu, który ma wykrywać przeglądarkę i w zależności od nazwy przekierowywać na odpowiednią stronę. Nieco rozszeżyłem swoje zadanie tworząc obiekt Java Script, który zwraca zarówno nazwę jak i wersję programu, z którego korzysta internauta. Obiekt wykrywa następujące programy: Internet Exploler, Mozilla / Mozilla Firefox, Opera.
  Całość opiera się na obiekcie **navigator** dostarczanym przez JS. Zainteresowanych zapraszam do zapoznania się z kodem.
title: Wykrywanie przeglądarki
url: /2005/06/08/wykrywanie-przegldarki/

---
Diziaj znajomy grafik poprosił mnie o stworzenie skryptu, który ma wykrywać przeglądarkę i w zależności od nazwy przekierowywać na odpowiednią stronę. Nieco rozszeżyłem swoje zadanie tworząc obiekt Java Script, który zwraca zarówno nazwę jak i wersję programu, z którego korzysta internauta. Obiekt wykrywa następujące programy: Internet Exploler, Mozilla / Mozilla Firefox, Opera.
Całość opiera się na obiekcie **navigator** dostarczanym przez JS. Zainteresowanych zapraszam do zapoznania się z kodem.

```js
/**
 * Wykrywacz przegladarki
 * autor: Łukasza Dywicki
 *
 * licencja: freeware
 * 7.06.2005
 **/

/**
 * @var array
 * Adresy, gdzie beda kierowane poszczegolne przegladarki
 **/
var locations = new Array();
locations['IE'] = 'index_ie.html';
locations['Firefox'] = 'index_ff.html';
locations['undefined'] = 'index_inne.html';

/**
 * @var object
 * obiekt do wykrywania nazwy i wersji przegladarki
 * Uzycie:
 * p = browser.metoda()
 **/
var browser = {
        /**
         * Metoda zwracajaca nazwe przegladarki
         * @return string
         **/
        getName : function() {
                switch(navigator.appName){
                        case 'Netscape':
                                if( navigator.userAgent.indexOf('Firefox') ) {
                                        this.name = 'Firefox';
                                } else {
                                        this.name = 'Mozilla';
                                }
                        break;
                        case 'Microsoft Internet Explorer':
                                this.name = 'IE';
                        break;
                        case 'Opera':
                                this.name = 'Opera';
                        break;
                        defalut:
                                this.name = 'undefined';
                        break;
                }
                return this.name;
        },

        /**
         * Metoda zwracajaca wersje przegladarki
         * @return string|integer
         **/
        getVersion : function() {
                if( !this.name ) {
                        this.getName();
                }
                if( this.name == 'Mozilla' ) {
                        this.version = parseFloat(navigator.appVersion);
                } else if( this.name == 'Firefox' ) {
                        temp = navigator.userAgent.split('Firefox/');
                        this.version = parseFloat(temp[1]);
                } else if( this.name == 'IE' ) {
                        temp = navigator.appVersion.split("MSIE")
                        this.version = parseFloat(temp[0]);
                } else if( this.name == 'Opera' ) {
                        this.version = parseFloat(navigator.appVersion);
                } else {
                        this.version = 'undefined';
                }
                return this.version;
        }
}

browser.getVersion();

/**
 * Funkcja ktora przekierowywuje
 **/
function detectBrowser() {
        n = browser.name;
        location.href = locations[n] ? locations[n] : locations['undefined'];
}
```

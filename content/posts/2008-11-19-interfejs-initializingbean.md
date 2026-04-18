---
author: splatch
category:
  - java
  - porady
  - spring
date: "2008-11-19T20:12:26+00:00"
draft: "true"
guid: http://blog.dywicki.pl/?p=234
title: Interfejs InitializingBean
url: /

---
Każdy z obiektów który jest konfigurowany w kontekście Springa ma szansę zweryfikować swój stan tuż po zainicjowaniu wszystkich wartości, które zostały mu przekazane. Wynika to z tego, że czasami obiekty potrafią działać na kilku różnych zasobach i można wstrzyknąć do nich tylko jeden rodzaj tegoż. Czasami po prostu potrzebujemy sprawdzić czy są przekazane wszystkie ustawienia konfiguracyjne bądź zainicjować połączenie do bazy danych na podstawie przekazanych parametrów.

Mamy wówczas do wyboru dwie metody. Pierwszą z nich jest implementacja interfejsu [org.springframework.beans.factory.InitializingBean](http://www.docjar.com/docs/api/org/springframework/beans/factory/InitializingBean.html). Jeśli wyrzuci ona jakiś wyjątek to Spring wyrzuci wyjątek wyżej i wstrzyma powoływanie kontekstu. Oto przykład:

```java
package org.code_house.samples.spring;

import java.util.Properties;

import org.springframework.beans.factory.BeanInitializationException;
import org.springframework.beans.factory.InitializingBean;

public class PropertiesConfiguration implements InitializingBean {

    /**
     * Ustawienia konfiguracyjne.
     */
    private Properties properties;

    /**
     * {@inheritDoc}
     */
    public void afterPropertiesSet() throws Exception {
        if (properties == null) {
            throw new BeanInitializationException("Nie przekazano ustawień "
                +"konfiguracyjnych. Pole properties jest wymagane.");
        }
    }
}
```

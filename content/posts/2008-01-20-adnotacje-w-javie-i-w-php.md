---
author: splatch
category:
  - java
  - php
date: "2008-01-20T21:55:28+00:00"
guid: http://blog.dywicki.pl/2008/01/20/adnotacje-w-javie-i-w-php/
summary: 'Parę dni wstecz trafiłem na ciekawe rozszerzenie [Reflection API](http://pl2.php.net/manual/en/language.oop5.reflection.php) do PHP. Umożliwia ono używanie tagów [phpdoc](http://phpdoc.org) w charakterze adnotacji. Rozwiązanie te jest zbliżone do znanego mi z Javy [xdocleta](http://xdoclet.sourceforge.net/). Drobną, aczkolwiek zauważalną, różnicą jest fakt, że xdoclet był odpalany przed kompilacją kodu ponieważ bytecode nie zawierał adnotacji. Oczywiście po wejściu Javy 5 xdoclet podupadł, jako coś co wyparły właśnie adnotacje. Może dla tych, którzy nie mieli okazji spotkania sie z takim rozwiązaniem drobny przykład kodu w Javie:'
title: Adnotacje w Javie i w PHP
url: /2008/01/20/adnotacje-w-javie-i-w-php/

---
Parę dni wstecz trafiłem na ciekawe rozszerzenie [Reflection API](http://pl2.php.net/manual/en/language.oop5.reflection.php) do PHP. Umożliwia ono używanie tagów [phpdoc](http://phpdoc.org) w charakterze adnotacji. Rozwiązanie te jest zbliżone do znanego mi z Javy [xdocleta](http://xdoclet.sourceforge.net/). Drobną, aczkolwiek zauważalną, różnicą jest fakt, że xdoclet był odpalany przed kompilacją kodu ponieważ bytecode nie zawierał adnotacji. Oczywiście po wejściu Javy 5 xdoclet podupadł, jako coś co wyparły właśnie adnotacje. Może dla tych, którzy nie mieli okazji spotkania sie z takim rozwiązaniem drobny przykład kodu w Javie:
```java
package pl.dywicki.swe.domain;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity(name = "user")
@Table(name = "user", schema = "public")
public class User {

  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  @Column(name = "user_id")
  private long id;

  @Column(name="user_login")
  private String login;

  @Column(name="user_password")
  private String password;

  public long getId() {
    return id;
  }

  public void setId(long id) {
    this.id = id;
  }

  public String getLogin() {
    return login;
  }

  public void setLogin(String login) {
    this.login = login;
  }

  public String getPassword() {
    return password;
  }

  public void setPassword(String password) {
    this.password = password;
  }

}
```

Co umożliwia takie rozwiązanie? Przede wszystkim dzięki zastosowaniu adnotacji spada ilość dodatkowych plików zawierających konfiguracje i mapowania. Oczywiście nie da się ich dzięki temu rozwiązaniu wykluczyć zupełnie, aczkolwiek da się znacznie ograniczyć ich użycie. Wielką zaletą, o której do tej pory nie wspomniałem, w przypadku Javy, jest to, że adnotacje możemy odczytywać w czasie uruchamiania programu a nie przed jego kompilacją.

### Adnotacja od strony technicznej

Jak można zadeklarować własną adnotację? Służy do tego specjalna konstrukcja:
```java
public @interface Name {
   String value() default "anonymous";
}
```
Użycie takiej adnotacji:
```java
@Name // poprawnie
public class Component {}

@Name("user.session") // również poprawnie
public class User {}
```

Możemy również przekazać kilka wartości gdy zadeklarujemy w adnotacji odpowiednie pole:
```java
public @interface Relation {
   String[] belongsTo();
   String offer();
}
```
Użycie takiej adnotacji:
```java
@Relation(belongTo = {"users", "groups"}, offer = "privileges")
public class PrivilegesEntityMapping {}
```

Warto tutaj wspomnieć o dwóch magicznych umowach. Po pierwsze atrybut o nazwie **value** jest domyślnym przypisaniem wartości podanej w nawiasach. Tzn przy wywołaniu @Name("user.session") możemy pobrać atrybut poprzez **annotationInstance.value()**. Po drugie dodanie po nazwie pola **default "wartość"** pozwala na pominięcie atrybutu przy definicji. Z ograniczeń, jakie mają adnotacje w Javie - wartościami atrybutów mogą być ciągi znaków, typy (klasy) oraz typy wyliczeniowe (enumy).

Jak użyć adnotacji? Korzystając z reflection api.
```java
Name instance = getClass().getAnnotation(Name.class); // Name.class to typ naszej adnotacji.
System.out.println(instance.value());
```

### Adnotacje a PHP

Jak wcześniej wspominałem, adnotacje nie są elementem PHP, chociaż ludzie pokusili się już o zbudowanie mechanizmu podobnego do tego, który jest w Javie. Jednym z takich projektów jest [Addendum](http://code.google.com/p/addendum/). Warto zaznaczyć, że nie jest to nazwa wyssana z palca a słowo "addendum" ma swoje znaczenie również w języku polskim i ogólnie rzecz biorąc znaczy dodatek do jakiegoś tekstu (więcej informacji na [wikisłowniku](http://pl.wiktionary.org/wiki/addendum)).

Jak wygląda użycie adnotacji w PHP? Jednym z przykładów może być [Torpedeo](http://code.google.com/p/torpedeo/). Projekt tego samego autora co Addendum, będący implementacją lekkiego ORMa. Przykładowa deklaracja obsługiwana przez Torpedeo:
```php
<?php
/**
 * @Table('nodes')
 */
class Node {
  /** @Integer */
  public $id;

  /** @String */
  public $title;

  /**
   * @Integer
   * @Nullable
   * @Column('parent_id');
   */
  public $parentId;

  /** @Boolean */
  public $visible;
}
?>
```

Addendum wymaga by klasy adnotacji rozszerzały klasę Annotation:
```php
<?php
class Nullable extends Annotation {}
class Transient extends Annotation {}
?>
```
Dodatkowo biblioteka dostarcza rozszerzeń reflection API z PHP5 umożliwiające odczytywanie adnotacji. [Dokładny tutorial](http://code.google.com/p/addendum/wiki/ShortTutorialByExample) jak używać tego narzędzia można znaleźć na [oficjalnej stronie projektu](http://code.google.com/p/addendum/).

### Adnotacje w PHP?

Pytanie czy adnotacje w PHP przyjmą się tak samo dobrze jak w Javie? Zdaje się, że by o tym mówić jest zbyt wcześnie, ponieważ projektów, które korzystają z tego mechanizmu jest na chwilę obecną bardzo mało. Sądząc jednak po tym jak szybko rozwinął się xdoclet można twierdzić, że to tylko kwestia czasu. Chociaż.. może w Javie po prostu jest więcej plików konfiguracyjnych?

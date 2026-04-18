---
author: splatch
category:
  - java
  - php
date: "2008-12-09T16:10:00+00:00"
guid: http://blog.dywicki.pl/?p=242
summary: Java od wersji 5.0 zawiera możliwość definiowania typów wyliczeniowych. Jednym z praktycznych przykładów zastosowania tego mechanizmu jest [TimeUnit](http://www.docjar.com/docs/api/java/util/concurrent/TimeUnit.html). Enum ten służy do konwertowania jednostek czasu pomiędzy różnymi wielkościami - na przykład z minut na sekundy.
title: Typ wyliczeniowy TimeUnit w Javie oraz klasa w PHP
url: /2008/12/09/timeunit-enum-in-java-and-class-inphp/

---
Java od wersji 5.0 zawiera możliwość definiowania typów wyliczeniowych. Jednym z praktycznych przykładów zastosowania tego mechanizmu jest [TimeUnit](http://www.docjar.com/docs/api/java/util/concurrent/TimeUnit.html). Enum ten służy do konwertowania jednostek czasu pomiędzy różnymi wielkościami - na przykład z minut na sekundy.

```java
import java.util.concurrent.TimeUnit;
// ...
TimeUnit.MINUTES.toSeconds(55);
```

Fajna sprawa - jeśli mamy do czynienia z konwersjami. Inny pożyteczny przykład to przekazywanie czasu jako argumentu:
```java
package org.code_house.concurrent;

import java.util.Date;
import java.util.concurrent.TimeUnit;

public class Lock {
    void lock(TimeUnit unit, long value) throws InterruptedException {
        Thread.sleep(unit.toMillis(value));
    }

    public static void main(String[] args) throws Exception {
        System.out.println("before " + new Date());

        new Lock().lock(TimeUnit.SECONDS, 30);

        System.out.println("after " + new Date());
    }

}
```
Programista, który używa takiej klasy nie musi się więc przejmować konwertowaniem jednostek na milisekundy, które są podstawą do wywołania metody Thread.sleep.

TimeUnit obsługuje następujące wielkości:

- nanosekundy
- mikrosekundy
- milisekundy
- sekundy
- minuty
- godziny
- dni

Posługując się kodem z Javy postanowiłem sobie przypomnieć nieco PHP i napisałem wersję tego typu wyliczeniowego dla PHP:
```php
<?php
// namespace org\code_house\util;
/**
 * A simple class for converting between time units.
 *
 * @author Łukasz Dywicki
 */
class TimeUnit {

    /**
     * Nanoseconds.
     */
    const NANOS     = 0;

    /**
     * Microseconds.
     */
    const MICROS    = 1;

    /**
     * Milliseconds
     */
    const MILLIS    = 2;

    /**
     * Seconds.
     */
    const SECONDS   = 3;

    /**
     * Minutes.
     */
    const MINUTES   = 4;

    /**
     * Hours.
     */
    const HOURS     = 5;

    /**
     * Days.
     */
    const DAYS      = 6;

    /**
     * Conversion table between time units.
     * @var array
     */
    private static $modifiers = array(
         self::NANOS     => 0.000000001,
         self::MICROS    => 0.000001,
         self::MILLIS    => 0.001,
         self::SECONDS   => 1,
         self::MINUTES   => 60,
         self::HOURS     => 3600,
         self::DAYS      => 86400,
    );

    /**
     * Constructs new time unit.
     *
     * @param $type TimeUnit Time unit base.
     */
    public function __construct($type) {
        if ($type < TimeUnit::NANOS || $type > TimeUnit::DAYS) {
            throw new RuntimeException('Time unit base is out of range');
        }
        $this->type = $type;
    }

    public function toNanos($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::NANOS);
    }

    public function toMicros($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::MICROS);
    }

    public function toMilis($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::MILLIS);
    }

    public function toSeconds($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::SECONDS);
    }

    public function toMinutes($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::MINUTES);
    }

    public function toHours($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::HOURS);
    }

    public function toDays($value) {
        return TimeUnit::convert($this->type, $value, TimeUnit::DAYS);
    }

    /**
     * Convert method.
     *
     * @param $from Time unit
     * @param $value Number of values.
     * @param $to Time unit
     * @return decimal
     */
    private final static function convert($from, $value, $to) {
        if ($from === $to) {
            return $value;
        }

        $value *= self::$modifiers[$from];
        return $value / self::$modifiers[$to];
    }
}

// przykład użycia
$unit = new TimeUnit(TimeUnit::SECONDS);
echo $unit->toMinutes(60) ."\n";

$unit = new TimeUnit(TimeUnit::HOURS);
echo $unit->toMinutes(1) ."\n";

?>
```

Niestety brak pól z modyfikatorami **public static final** w PHP uniemożliwił zastosowanie choćby czegoś podobnego do typu wyliczeniowego.. Kod klasy dla PHP jest wolnodostępny. :-)

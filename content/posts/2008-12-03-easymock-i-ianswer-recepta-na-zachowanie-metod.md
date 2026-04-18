---
author: splatch
category:
  - java
  - porady
  - testy-jednostkowe
date: "2008-12-03T16:06:42+00:00"
guid: http://blog.dywicki.pl/?p=240
summary: Złym zwyczajem jest modyfikowanie argumentów zamiast zwracania nowej wartości, jakkolwiek trafiają się sytuacje gdy testowany kod powinien weryfikować takie wywołania. W EasyMock mamy do dyspozycji w takim przypadku interfejs IAnswer. Jego użycie jest w miarę proste - dobieramy się do tablicy argumentów i robimy z nią co potrzeba.
title: EasyMock i IAnswer, recepta na zachowanie metod
url: /2008/12/03/easymock-inaswer-as-method-behaviour-recipe/

---
Złym zwyczajem jest modyfikowanie argumentów zamiast zwracania nowej wartości, jakkolwiek trafiają się sytuacje gdy testowany kod powinien weryfikować takie wywołania. W EasyMock mamy do dyspozycji w takim przypadku interfejs IAnswer. Jego użycie jest w miarę proste - dobieramy się do tablicy argumentów i robimy z nią co potrzeba.

Oto przykład - interfejs Populator dodaje do przekazanej tablicy określoną wartość w puste miejsca. Zwraca też ilość zmian, które zaprowadził. Całość jest oparta na bardzo prostym kodzie, zaledwie jedna pętla, jakkolwiek na potrzeby przykładu jest to wystarczające. Pragnę jednak nadmienić, że w podobny sposób można testować bardziej złożony kod, gdzie kolaborują ze sobą dwa obiekty - tj. Observator i Observable czy też Visitor i Visitable.

```java
package org.code_house.test.mock;

interface Populator {
    int fill(String value, String[] arguments);
}
```

Implementacja interfejsu jest bardzo prosta:
```java
package org.code_house.test.mock;

class FillAnswer implements IAnswer<Integer> {
    public Integer answer() throws Throwable {
        // pobranie argumentow
        Object[] arguments = EasyMock.getCurrentArguments();
        String name = (String) arguments[0];
        Object[] values = (Object[]) arguments[1];

        // logika potrzebna do testu
        int populated = 0;
        for (int i = 0; i < values.length; i++) {
            if (values[i] == null) {
                populated++;
                values[i] = name;
            }
        }
        return populated;
    }
}
```

Test jest również niezbyt skomplikowany - bazuje on na poprzedniej nocie - "Testowanie tablic argumentów z EasyMock":

```java
package org.code_house.test.mock;

import static org.easymock.EasyMock.*;
import org.easymock.IAnswer;

import java.util.Arrays;

import junit.framework.TestCase;

public class AnswerTest extends TestCase {

    public void testPopulate() {
        Populator populator = createMock(Populator.class);
        expect(populator.fill(eq("Code House"), aryEq(new String[3])))
            .andAnswer(new FillAnswer());
        expect(populator.fill(eq("Code House"), aryEq(new String[] {"1"})))
            .andAnswer(new FillAnswer());
        replay(populator);

        // tablica do wypełnienia
        String[] populated = new String[3];
        int added = populator.fill("Code House", populated);
        assertEquals(3, added);
        // w odpowiedzi oczekujemy dodania 3 nowych elementów
        assertTrue(Arrays.equals(
            new String[] {"Code House", "Code House", "Code House"},
            populated
        ));

        // w tym przypadku nie oczekujemy nowości
        added = populator.fill("Code House", new String[] {"1"});
        assertEquals(0, added);
    }
}
```

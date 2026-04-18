---
author: splatch
category:
  - java
  - porady
  - testy-jednostkowe
date: "2008-11-26T06:00:02+00:00"
guid: http://blog.dywicki.pl/?p=239
summary: Często zdarza się że metody, które piszemy i później testujemy mają argumenty w postaci tablic. EasyMock wówczas potrafi zgłosić wyjątek, że przekazana tablica jest różna od oczekiwanej mimo, że zawartość tablic jest identyczna.
title: Testowanie tablic argumentów z EasyMock
url: /2008/11/26/testing-array-parameters-with-easymock/

---
Często zdarza się że metody, które piszemy i później testujemy mają argumenty w postaci tablic. EasyMock wówczas potrafi zgłosić wyjątek, że przekazana tablica jest różna od oczekiwanej mimo, że zawartość tablic jest identyczna.

```java
java.lang.AssertionError:
  Unexpected method call find([Ljava.lang.String;@1ad77a7):
    find([Ljava.lang.String;@b8f82d): expected: 1, actual: 0
```

```java
package org.code_house.test.mock;

import static org.easymock.EasyMock.aryEq;
import static org.easymock.EasyMock.createMock;
import static org.easymock.EasyMock.expect;
import static org.easymock.EasyMock.replay;

import java.util.Collections;
import java.util.List;

import junit.framework.TestCase;

interface Finder {
    List<String> find(String[] names);
}

public class FinderTest extends TestCase {

    public void testFind() {
        Finder mock = createMock(Finder.class);
        expect(mock.find(aryEq(new String[] {"Amy", "Luke"}))).andReturn(
            Collections.EMPTY_LIST);
        replay(mock);

        List<String> list = mock.find(new String[] {"Amy", "Luke"});
        assertTrue(list.isEmpty());
    }
}

```

Rozwiązaniem tych problemów jest użycie mechanizmu "matcherów", które są wbudowane w sam szkielet. Najprostszym sposobem na ich użycie jest odwołanie przy pomocy statycznego wywołania którejś z metod EasyMock. Poniżej znajduje się lista dostępnych matcherów. Wartość "given" to argument przekazany w wywołaniu metody.

- **eq**(X value)  

   Argument pasuje jeśli wyrażenie value.equals(given) zwróci true. Dostępny dla wszystkich typów prostych i obiektów.
- **anyBoolean**(), **anyByte**(), **anyChar**(), **anyDouble**(), **anyFloat**(), **anyInt**(), **anyLong**(), **anyObject**(), **anyShort**()  

   Dowolna wartość zostanie przepuszczona niezależnie od tego co będzie przekazane. Dostępny dla wszystkich typów.

- **eq**(X value, X delta)  

   Pasuje jeśli aktualna jeśli wyrażenie given jest równe value z dokładnością do delta. Dostępne dla typów float oraz double.
- **aryEq**(X value)  

   Pasuje jeśli wyrażenie Arrays.equals(value, given) zwraca true. Dostępne dla tablic typów prostych jak i dla obiektów.
- **isNull**()  

   Pasuje jeśli given == null. Dostępne tylko dla obiektów.
- **notNull**()  

   Pasuje jeśli given != null. Dostępne tylko dla obiektów.
- **same**(X value)  

   Pasuje jeśli wyrażenie value == given jest prawdziwe. Dostępne tylko dla obiektów.
- **isA**(Class clazz)  

   Pasuje jeśli given instanceof clazz zwraca true. Given może być instancję clazz jak i pochodną.
- **lt**(X value), **leq**(X value), **geq**(X value), **gt**(X value)  

   Zwraca true jeśli given jest mniejsze, mniejsze równe, większe równe bądź większe niż value. Dostępne dla wszystkich numerycznych typów prostych.
- **startsWith**(String prefix), **contains**(String substring), **endsWith**(String suffix)  

   Zwraca true jeśli given zaczyna się, zawiera bądź kończy się daną wartością. Dostępne dla argumentów typu String.
- **matches**(String regex), **find**(String regex)  

   Pasuje jeśli given pasuje w całości do wyrażenia/fragment pasuje. Dostępne dla argumentów typu String.
- **and**(X first, X second)  

   Zwraca true jeśli matcher first oraz second zwracają prawdę.
- **or**(X first, X second)  

   Zwraca true jeśli matcher first bądź second pasuje.
- **not**(X value)  

   Zwraca true jeśli matcher value zwrócił fałsz.

Uzbrojeni w taki zestaw matcherów możemy poprawić kod testu tak by był on poprawny:
```java
public class FinderTest extends TestCase {

    public void testFind() {
        Finder mock = createMock(Finder.class);
        expect(mock.find(aryEq(new String[] {"Amy", "Luke"}))).andReturn(
            Collections.EMPTY_LIST);
        replay(mock);

        List<String> list = mock.find(new String[] {"Amy", "Luke"});
        assertTrue(list.isEmpty());
    }
}
```

Inny przykład:
```java
public class FinderTest extends TestCase {

    public void testFind() {
        Finder mock = createMock(Finder.class);
        expect(mock.find("Lucy", aryEq(new String[] {"Amy", "Luke"}))).andReturn(
            Collections.EMPTY_LIST);
        replay(mock);

        List<String> list = mock.find("Lucy", new String[] {"Amy", "Luke"});
        assertTrue(list.isEmpty());
    }
}
```

Warto zwrócić uwagę na to, że jeśli korzystamy dla matchera aby dopasować argument to musimy zrobić to dla wszystkich, inaczej poleci wyjątek:
```java
java.lang.IllegalStateException: 2 matchers expected, 1 recorded.
```

Stąd nasz test w końcowej postaci powinien wyglądać następująco:
```java
package org.code_house.test.mock;

import static org.easymock.EasyMock.aryEq;
import static org.easymock.EasyMock.createMock;
import static org.easymock.EasyMock.eq;
import static org.easymock.EasyMock.expect;
import static org.easymock.EasyMock.replay;

import java.util.Collections;
import java.util.List;

import junit.framework.TestCase;

interface Finder {
    List<String> find(String[] names);

    List<String> find(String name, String[] names);
}

public class FinderTest extends TestCase {

    public void testFind() {
        Finder mock = createMock(Finder.class);
        expect(mock.find(eq("Lucy"), aryEq(new String[] {"Amy", "Luke"}))).andReturn(
            Collections.EMPTY_LIST);
        replay(mock);

        List<String> list = mock.find("Lucy", new String[] {"Amy", "Luke"});
        assertTrue(list.isEmpty());
    }
}
```

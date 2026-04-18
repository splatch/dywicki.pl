---
author: splatch
category:
  - mvc
  - php
date: "2007-03-18T21:57:05+00:00"
guid: http://blog.dywicki.pl/2007/03/18/obiekty-biznesowe-w-aplikacji/
summary: |-
  ### Pierwsze błędy

  Pamiętam swoje pierwsze implementacje MVC, w czasach gdy słowo framework nie było jeszcze trendy a wiele osób, w tym i ja, nawet go nie używało. W owych pierwszych implementacjach MVC model był pewnego rodzaju fasadą, która zapewniała dostęp do danych.
  Problem polegał na tym, że kod np klasy User wyglądał następująco:
title: Obiekty biznesowe w aplikacji.
url: /2007/03/18/obiekty-biznesowe-w-aplikacji/

---
### Pierwsze błędy

Pamiętam swoje pierwsze implementacje MVC, w czasach gdy słowo framework nie było jeszcze trendy a wiele osób, w tym i ja, nawet go nie używało. W owych pierwszych implementacjach MVC model był pewnego rodzaju fasadą, która zapewniała dostęp do danych.
Problem polegał na tym, że kod np klasy User wyglądał następująco:

```php
<?php
class User extends Model {
    function User(DB $db) {
        parent::Model($db);
    }

    function getById($id) {
        $sql = 'SELECT * FROM user WHERE user_id = '. $id;
        return $db->query($sql);
    }
}
?>
```
Jak widać na powyższym listingu klasa jest odpowiedzialna za pobieranie danych. Problem takiej implementacji polega na tym, że wciąż operujemy na tablicach bądź na ResultSetach zwracanych przez obiekt odpowiedzialny za wykonywanie zapytań. Kod w takim wykonaniu jest mocno związany z bazą danych i jest ciężki do użycia w innych projektach. Co więcej w pierwszym podejściu nie korzystałem z wielu możliwości jakie daje programowanie obiektowe i po prostu odwołania do klasy nadrzędnej czy też metody abstrakcyjne były co najmniej sporadyczne. Także, każdy "model" miał swoją metodę getById, która miała swoje zapytanie. Jest to najprostsza implementacja, która zapewnia tylko jedno - wyłączenie zapytań do oddzielnych plików.

### Wzorce

Znacznie lepszym krokiem byłoby wykorzystanie możliwości chociażby dziedziczenia i użyć nazwanego przez [Martina Fowlera](http://martinfowler.com) wzorca [ActiveRecord](http://martinfowler.com/eaaCatalog/activeRecord.html), który zapewnia nam mapowanie pojedynczej encji do klasy.

Problem, jaki powstaje przy użyciu ActiveRecordu to niestety uzależnienie kodu od wybranej implementacji wzorca, co więcej kod będzie nieprzenośny, np nie da się go użyć bez zewnętrznych bibliotek. Zatem problem pozostaje taki jak był wcześniej - logika, która była związana z danymi (np przeliczanie wartości jednego pola w zależności od drugiego) została zawarta w modelu staje się bezużyteczna.

Problemy te można rozwiązać wyraźnie oddzielając obiekty z których korzysta aplikacja od warstwy dostępu do nich. Tutaj rodzi się pojęcie [Domain Model](http://martinfowler.com/eaaCatalog/domainModel.html) czy też Domain Object. Przede wszystkim jest to kod, który jest niezależny od źródła danych np obiekt Invoice możemy pobrać zarówno z bazy jak i z zewnętrznego systemu powiedzmy przez usługę sieciową. Obiekt zawsze zachowa się w ten sam sposób i jego użycie nie będzie zależne od miejsca z którego je uzyskaliśmy. Idealnym przykładem implementacji Domain Modelu są np beany utrwalane przy pomocy [Hibernate](http://hibernate.org). Mamy kod, który jest przenośny, w którym nie ma żadnych jawnych odwołań do informacji zawartych poza "obiektami domenowymi", jest ona po prostu autonomiczna.

Ciężko jest określić czym w pierwszej kolejności się zająć bazą czy obiektami biznesowymi? Wydaje mi się, że modelując najpierw bazę danych ciężko jest wychwycić dziedziczenia pomiędzy obiektami biznesowymi i najzwyczajniej w świecie zostaną one pominięte. Z drugiej strony tworząc najpierw obiekty biznesowe możemy natrafić na trudności z odwzorowaniem całej, nie raz, złożonej struktury do encji w bazie.

### Data Access Objects

Dostęp warstwy z obiektami domenowymi realizujemy przez Data Access Objects. Jest to zespół klas, które zapewniają odczyt, usuwanie oraz zapisywanie obiektów (całość modyfikujemy przez dostępne w obiektach domenowych metody). DAO ma na celu zapewnienie jednolitego interfejsu dla dostępu do obiektów bez pojawiania się kodu specyficznego dla danego źródła danych, niezależnie czy to będzie baza danych czy usługa sieciowa.

### Service Layer

Warstwa usług to miejsce przecięć różnych źródeł danych. Granica między DAO a Service Layer jest dość istotna - DAO obejmuje transakcje systemowe podczas gdy Service Layer transakcje biznesowe. Może słowo wyjaśnienia na temat tych dwóch rodzai transakcji - pierwsza jest zwykle kontrolowana przez bazę danych i zawiera ciąg poleceń SQL. Transakcja biznesowa obejmuje jedną konkretną operację np przelew bankowy - to logowanie, określenie kwoty i odbiorcy, weryfikacja kodu jednorazowego oraz zablokowanie środków. Powodzenie takiej operacji zależy od wielu czynników w tym od systemów zewnętrznych.
Warstwa usług jest bardzo przydatna w chwili gdy z tej samej logiki korzysta więcej niż jeden system. Niestety nie mam wielkiego doświadczenia z takimi sytuacjami i nie czuję się na siłach by o tym pisać, a to co tu przytoczyłem to głównie to co pamiętam z PoEAA.

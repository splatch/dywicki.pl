---
author: splatch
category:
  - wiadomości
date: "2007-03-18T20:33:03+00:00"
draft: "true"
guid: http://blog.dywicki.pl/?p=115
title: Group Layout
url: /

---
Group layout używa dwóch rodzajów kontenerów:
\- sequential
\- parallel
które można dowolnie zagnieżdżać.

W kontenerze "sequential" komponenty są ustawiane jeden za drugim (zupełnie jak w BoxLayout/FlowLayout) wzdłuż jednej osi. Pozycja kolejnego elementu jest relatywna względem poprzedniego.

W kontenerze typu "parallel" komponenty sa umieszczane jeden pod drugim. Mogą one być wyrównane względem trzech wartości: baseline, top, bottom (zaraz przy osi). W zależności od potrzeb komponenty mogą być wyrównane do środka, lewej bądź prawej strony, jeżeli nie są tego samego rozmiaru.

Zwykle komponenty są ustawiane w jednej osi jako sequential a w drugiej jako parallel, co wyklucza konflikty i nakładanie się elementów.

Oprócz wcześniej wymienionych elementów istnieją jeszcze dodatkowe, mianowicie grupy. Grupa jest kontenerem zawierającym kolejne komponenty i jest pozycjonowana jako całość.

Rozmiarem grupy typu sequential jest suma rozmiarów wszystkich komponentów w danej osi podczas gdy grupa typu parallel używa rozmiaru największego elementu.

Sprężynki :)
Sprężynka jest niewidocznym elementem o pewnym rozmiarze. Dodając te elementy możesz dokładnie określać odległości pomiędzy granicami komponentów.

GroupLayout definiuje również automatyczne sprężynki na podstawie preferowanej odległości pomiędzy graniczącymi komponentami (bądz pomiędzy granicą komponentu a granicą kontenera). Rozmiar jaki ma sprężynka jest liczony dynamicznie na podstawie używanego przez aplikacje Look & Feela (używana klasa LayoutStyle).

Korzystając z GroupLayout możesz określać dwa rodzaje relacji na podstawie których będą tworzone sprężynki:
\- sprężynka pomiędzy komponentami (tworzymy metodą addPrefferredGap)
\- sprężynka pomiędzy komponentem a kontenerem (tworzymy metodą addContainerGap)
Następnie są trzy typy tychże sprężynek:
\- relatywne
\- nierelatywne
\- wcięcia

Typ wyliczeniowy LayoutStyle.ComponentPlacement zawiera wartości którymi określa się typ sprężynki przy jej tworzeniu przez metodę addPreferredGap. Są to wartośći RELATED, UNRELATED oraz INDENT.
Różnica pomiędzy sprężynką relatywną a nierelatywną leży tylko przy rozmiarze. Odległość pomiędzy nierelatywnymi komponentami jest znacznie większa.
Wcięcie zapewnia elementowi odpowiednie oddalenie wzdłuż danej osi względem komponentu, który znajduje się ponad nim.

Warto wspomnieć, że GroupLayout może automatycznie wstawiać sprężynki. Jeśli nie dodasz własnych sprężynek przy tworzeniu układu zostaną one dodane domyślnie jako related. Ta opcja nie jest domyślnie włączona.
Można ją włączyć poprzez dwie metody
\- GroupLayout.setAutoCreateGaps(true)
\- GroupLayout.setAutoCreateContainerGaps(true)

Rozmiar komponentów oraz ich skalowanie
Nie ma ograniczeń co do ilości komponentów, które są komponowane poprzez layout. Jakkolwiek możesz określić trzy wartości dla każdego elementu: rozmiar minimalny, maksymalny oraz preferowany.
Jeśli wartości te nie zostaną określone przy tworzeniu układu (dodatkowe argumenty metody addComponent) to są one pobierane z komponentu poprzez odpowiednie akcesory.

GroupLayout określa stałe, które umożliwiają ustawienie wcześniej wspomnianych wielkości. Mogą być one użyte jako argumenty metody
addComponent(Component comp, int min, int pref, int max).
Dostępne kombinacje:
\- Włączenie skalowania (włączając "tonięcie" elementów oraz ich "wyciągnięcie")
group.addComponent(component, 0, GroupLayout.DEFAULT\_SIZE, Short.MAX\_VALUE)
\- Określenie rozmiaru na stałe, wyłączenie skalowania:
group.addComponent(component, GroupLayout.PREFERRED\_SIZE, GroupLayout.DEFAULT\_SIZE, GroupLayout.PREFERRED\_SIZE)

W przykładanych rozmiar początkowy komponentu nie jet określany, jest wykorzystywany preferowany rozmiar. Dla przykładu, możesz określić sprężynkę pomiędzy dwoma komponentami,

która rozpycha elementy bądź je przyciąga. Predefiniowana odległość pomiędzy komponentami jest używana tylko w przypadku całkowitego "zgniecenia sprężynki".
Przykładowy kod:
layout.createSequentialGroup()
.addComponent(c1)
// ta sprężynka będzie bardzo elastyczna :)
.addPreferredGap(LayoutStyle.ComponentPlacement.RELATED, GroupLayout.DEFAULT\_SIZE, Short.MAX\_VALUE)
.addComponent(c2);

Grupy typu parallel i skalowanie w ich wnętrzu
Elementy skalowane umieszczone w grupie typu parallel wypełniają przestrzeń zdefiniowaną przez największy komponent, zatem są zwykle tego samego rozmiaru co największy komponent. Nie mniej GroupLayout daje również możliwość określenia innej strategii skalowania wewnątrz grupy. Jeśli skalowanie grupy jest wyłączone to elementy w niej zawarte są rozciągane do preferowanego rozmiaru grupy. Dzięki temu możesz oddalać elementy względem jednej ze stron bądź określić własne rozmiary dla nich.
Spróbujmy określić taki sam rozmiar dla dwóch komponentów:
layout.createParallelGroup(GroupLayout.Alignment.LEADING, false)
.addComponent(c3, GroupLayout.DEFAULT\_SIZE, GroupLayout.DEFAULT\_SIZE, Short.MAX\_VALUE)
.addComponent(c4, GroupLayout.DEFAULT\_SIZE, GroupLayout.DEFAULT\_SIZE, Short.MAX\_VALUE);

Z kodu możemy odczytać, że:
\- Rozmiar grupy nadrzędnej jest ustawiony na rozmiar preferowany, co znaczy, że przyjmie ona rozmiar największego elementu.
\- Skalowane elementy mają ten sam rozmiar co grupa i nie są one rozciągane (drugi argument metody createParallelGroup to flaga resizable).

Pytanie dla pilnych czytelników: Dlaczego nie powiązać elementów na stałe? (Skoro pierwszy komponent jest większy niż drugi, to możemy je powiązać rozmiarami)
Odpowiedź: Ponieważ rozmiar jest zależny od platformy (L&F) oraz od języka aplikacji. W jednym języku pierwszy przycisk będzie większy w drugim mniejszy. Jeśli byśmy określili

na sztywno, że pierwszy komponent zawsze jest większy to nie był by on skalowany w chwili gdy drugi okazał by się pokaźniejszy.

Komponenty o tym samym rozmiarze
W poprzednim przypadku mieliśmy dwa komponenty, które były w grupie i miały ten sam rozmiar. Ale co w przypadku gdy mamy niepowiązane komponenty o tym samym rozmiarze? Przecież

nie zawsze można grupować elementy. Jako przykład posłużą nam przyciski ok i cancel znajdujące się w wierszu na dole okna.
Do obsługi takich przypadków GroupLayout dostarcza metody linkSize. Metoda ta pozwala na określenie tego samego rozmiaru komponentów bez względu na ich pozycję. Wszystkie

komponenty przyjmą rozmiar największego elementu.

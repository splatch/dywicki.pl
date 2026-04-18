---
author: splatch
category:
  - eclipse
  - java
  - jface
  - swt
date: "2007-06-07T08:58:34+00:00"
guid: http://blog.dywicki.pl/?p=139
summary: Jakiś czas temu [Michał Mech](http://michalmech.pl) [pisał](http://michalmech.pl/index.php/2007/05/23/manager-layoutu-grouplayout-w-java/) o tym jak można rozkładać komponenty w [Swingu](http://java.sun.com/docs/books/tutorial/uiswing/index.html) przy pomocy [Group Layoutu](http://java.sun.com/javase/6/docs/api/javax/swing/GroupLayout.html). Dzisiejszego dnia mam zamiar pokazać Wam drugą stronę medalu - mianowicie [Form Layout](http://help.eclipse.org/help31/nftopic/org.eclipse.platform.doc.isv/reference/api/org/eclipse/swt/layout/FormLayout.html), który można wykorzystać przy tworzeniu aplikacji w [SWT](http://eclipse.org/swt/).
title: Form Layout
url: /2007/06/07/swt-jface-form-layout/

---
Jakiś czas temu [Michał Mech](http://michalmech.pl) [pisał](http://michalmech.pl/index.php/2007/05/23/manager-layoutu-grouplayout-w-java/) o tym jak można rozkładać komponenty w [Swingu](http://java.sun.com/docs/books/tutorial/uiswing/index.html) przy pomocy [Group Layoutu](http://java.sun.com/javase/6/docs/api/javax/swing/GroupLayout.html). Dzisiejszego dnia mam zamiar pokazać Wam drugą stronę medalu - mianowicie [Form Layout](http://help.eclipse.org/help31/nftopic/org.eclipse.platform.doc.isv/reference/api/org/eclipse/swt/layout/FormLayout.html), który można wykorzystać przy tworzeniu aplikacji w [SWT](http://eclipse.org/swt/).

Ogólnie w Eclipse dominuje, chyba podobnie jak w Swingu, layout oparty na tworzeniu siatki - [Grid Layout](http://help.eclipse.org/help31/nftopic/org.eclipse.platform.doc.isv/reference/api/org/eclipse/swt/layout/GridLayout.html). Jest on w miarę elastyczny (zrobisz w nim wszystko to, czego potrzebujesz). Niestety wprowadzanie zmian w układzie (dodanie nowego elementu, zmiana pozycji istniejącego) wiąże się z przepisaniem większości kodu na nowo, ponieważ w "siatce" ma znaczenie kolejność dodawania. Gdy mamy siatkę dwukolumnową to trzeci komponent, który dodajemy wyląduje w drugim wierszu niezależnie od nas.

[![Nasza](/wp-content/uploads/2007/06/form-layout1.PNG)](/wp-content/uploads/2007/06/form-layout1.PNG "Nasza pierwsza kontrolka!")[Form Layout](http://help.eclipse.org/help31/nftopic/org.eclipse.platform.doc.isv/reference/api/org/eclipse/swt/layout/FormLayout.html) odrywa nas od siatki dając pełną dowolność w rozkładaniu kontrolek. Każdy komponent możemy ją pozycjonować na dwa sposoby - albo względem konturów kontenera (może to być okno, bądź grupa, w której się znajdujemy) albo względem innego komponentu. No, ale może po kolei.
[SWT](http://eclipse.org) jest zorganizowane inaczej niż Swing/AWT. Tutaj, by stworzyć jakiś komponent w większości przypadków jest potrzebny przodek, podczas gdy w Swingu nie potrzeba w sumie nic (wynika to z trzymania się specyfikacji [JavaBeans](http://java.sun.com/products/javabeans/), która zakłada, że fasolki powinny mieć puste konstruktory). Dodatkowo każdemu tworzonemu komponentowi należy nadać styl przy użyciu stałych z [klasy SWT](http://help.eclipse.org/help31/nftopic/org.eclipse.platform.doc.isv/reference/api/org/eclipse/swt/SWT.html). Jeśli nie chcemy żadnych dodatkowych efektów dajemy po prostu _SWT.NONE_. Może kilka przykładów

```java
FormData data = new FormData();
data.right = new FormAttachment(0, 50);

Label name = new Label(parent, SWT.NONE);
name.setText("Nazwa");
name.setLayoutData(data);
```
Powyższy fragment kodu spowoduje przyciągnięcie etykiety tekstowej do 50 piksela.

```java
FormData data = new FormData();
// przyklejamy pole do inntch komponentów
data.left = new FormAttachment(name);
data.right = new FormAttachment(righttable);
Text field = new Text(parent, SWT.SINGLE);
field.setText("Krotki opis");
field.setLayoutData(data);
```
Ten kawałek kodu spowoduje przyciągnięcie komponentu z lewej do etykiety tekstowej a z prawej do tabelki.

```java
FormData data = new FormData();
// ściągamy przycisk prawie na dno, bez 5px
data.bottom = new FormAttachment(100, -5);
// z prawej strony przycisk będzie 5px od środka kontenera
data.right = new FormAttachment(50, -5);

Button no = new Button(parent, SWT.NONE);
no.setText("Nie");
no.setLayoutData(data);

FormData data = new FormData();
data.bottom = new FormAttachment(100, -5);
// odsuwamy o 5px od środka - pierwszy przycisk jest o -5px od środka,
// więc dajemy 5, żeby dojść do 50% i 5 na rzeczywisty offset od centrum
data.left = new FormAttachment(no, 10);

Button yes = new Button(parent, SWT.NONE);
yes.setText("Tak");
yes.setLayoutData(data);
```
Ten fragment kodu spowoduje umieszczenie dwóch przycisków w odległości 10 pikseli od siebie. Przyznacie, że kod nie jest skomplikowany, prawda? :) Jest go troszkę, ale nie jest on ani trudny ani zawiły. Zmiany, które wprowadzamy najczęściej sprowadzają się do zmiany kilku FormAttachmentów.

Kompletny kod kontrolki widocznej na załączonym obrazku:
```java
package jug;

import org.eclipse.swt.SWT;
import org.eclipse.swt.layout.FormAttachment;
import org.eclipse.swt.layout.FormData;
import org.eclipse.swt.layout.FormLayout;
import org.eclipse.swt.widgets.Button;
import org.eclipse.swt.widgets.Display;
import org.eclipse.swt.widgets.Label;
import org.eclipse.swt.widgets.Shell;
import org.eclipse.swt.widgets.Table;
import org.eclipse.swt.widgets.Text;

/**
 * Kontrolka prezentująca możliwości {@link FormLayout} wraz z modyfikacjami
 * wprowadzonymi podczas prelekcji.
 *
 * @author Łukasz Dywicki
 */
public class FormLayoutDemo {

 private Display display;
 private Shell shell;

 public FormLayoutDemo() {
 display = new Display();
 shell = new Shell(display);
 pack();
 }

 /**
 * Inicjowanie komponentów i układu okienka
 */
 private void pack() {
 // główny bohater:
 FormLayout layout = new FormLayout();
 // dorzucamy marginesy
 layout.marginTop = 5;
 layout.marginLeft = 5;
 layout.marginRight = 5;
 layout.spacing = 5;

 shell.setLayout(layout);

 // tworzymy wszystkie komponenty
 Label name = new Label(shell, 0);
 Text field = new Text(shell, SWT.SINGLE | SWT.BORDER);
 Label description = new Label(shell, 0);
 Text area = new Text(shell, SWT.MULTI | SWT.BORDER);
 Button no = new Button(shell, SWT.PUSH);
 Button yes = new Button(shell, SWT.PUSH);
 Table righttable = new Table(shell, SWT.BORDER | SWT.V_SCROLL
 | SWT.H_SCROLL);

 // konfiguracja i pozycjonowanie elementów
 FormData data = new FormData();
 data.right = new FormAttachment(0, 50);

 name.setText("&Nazwa");
 name.setLayoutData(data);

 data = new FormData();
 data.left = new FormAttachment(name);
 data.right = new FormAttachment(righttable);

 field.setText("Krotki opis");
 field.setLayoutData(data);

 data = new FormData();
 data.right = new FormAttachment(0, 50);
 data.top = new FormAttachment(field, 5);

 description.setText("Napis");
 description.setLayoutData(data);

 data = new FormData();
 data.left = new FormAttachment(description); // przypięcie w danym
 // kierunku do danego
 // pola
 data.right = new FormAttachment(righttable);
 data.top = new FormAttachment(field, 5);
 data.bottom = new FormAttachment(yes); // przycisk pod polem

 area.setText("Witamy!");
 area.setLayoutData(data);

 data = new FormData();
 data.bottom = new FormAttachment(100, -5);
 data.right = new FormAttachment(50, -5);

 no.setText("Ni&e");
 no.setLayoutData(data);

 data = new FormData();
 data.bottom = new FormAttachment(100, -5);
 data.left = new FormAttachment(no, 10);

 yes.setText("Ta&k");
 yes.setLayoutData(data);

 data = new FormData();
 data.right = new FormAttachment(100);
 data.bottom = new FormAttachment(yes);
 // albo przyciągnięcie prawie do dna
 // data.bottom = new FormAttachment(100, -5);
 data.top = new FormAttachment(0);
 data.left = new FormAttachment(75);
 righttable.setLayoutData(data);
 }

 public void show() {
 shell.setBounds(50, 50, 300, 200);
 shell.open();
 while (!shell.isDisposed()) {
 if (!display.readAndDispatch())
 display.sleep();
 }
 display.dispose();
 }

 public static void main(String[] args) {
 new FormLayoutDemo().show();
 }
}
```

[![Osie..](/wp-content/uploads/2007/06/axis.PNG)](/wp-content/uploads/2007/06/axis.PNG "Osie..") Może jeszcze o tym jak w FormLayout zachowują się FormAttachemnty. Wszystkie komponenty pozycjonujemy w dwóch kierunkach (oś X, Y) przy użyciu 4 dostępnych stron. Jeśli jakiś element ma być rozciągnięty na całą szerokość kontrolki (oś X) to z lewej dajemy FormAttachment(0) a z prawej FormAttachment(100). Są to jak wcześniej wspominałem, wartości procentowe z osi X.
Jeżeli chcemy aby komponent był rozciągnięty na całą wysokość kontrolki postępujemy analogicznie - góra 0, dół 100. I teraz ważna informacja o offsetach. Gdy przesuwamy komponent zgodnie z grotem danej osi jest on dodatni, jeśli przeciwnie jest on ujemny. Offsety są podawane w pikselach jako drugi (opcjonalny) argument, zarówno przy pozycjonowaniu absolutnym jak i relatywnym. No i na koniec obrazek pokazujący osie. :)

Na koniec dorzucam [link do slajdów](/wp-content/uploads/2007/06/swt-i-jface-slajdy.pdf "swt i jface slajdy.pdf"), których użyłem [podczas prelekcji](http://groups.google.com/group/warszawa-jug/browse_thread/thread/e8a80ea94a8cf599) poprowadzonej w ramach spotkań [Warszawskiego JUGu](http://warszawa.jug.pl).

Ps. Ostatnimi czasy Wordpress zaczął zachowywać się bardzo dziwnie. Po wskoczeniu do jakiejś notki generował kanał RSS.. jeszcze nie wiem jaka jest tego przyczyna (podejrzewam aktualizację softu na serwerze), póki co zmieniły się linki. Proszę o aktualizację adresów do kanałów w swoich czytnikach. :)

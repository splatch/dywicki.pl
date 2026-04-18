---
author: splatch
category:
  - java
  - jaxb
  - xml
date: "2007-11-28T14:12:33+00:00"
guid: http://blog.dywicki.pl/?p=200
summary: Z ostatnich not zebrało mi się kilka obietnic. Jedną z nich było omówienie pluginów w wydaniu JAXB. Jako, że dzisiaj urządziłem sobie wolny dzień postanowiłem wywiązać się przynajmniej z części obowiązków z tym i z tych blogowych.
title: Rozszerzanie JAXB
url: /2007/11/28/rozszezanie-jaxb/

---
Z ostatnich not zebrało mi się kilka obietnic. Jedną z nich było omówienie pluginów w wydaniu JAXB. Jako, że dzisiaj urządziłem sobie wolny dzień postanowiłem wywiązać się przynajmniej z części obowiązków z tym i z tych blogowych.

Przykład, na którym do tej pory zawsze bazowałem to dodanie wsparcia dla obsługi zdarzeń z użyciem PropertyChangeSupport. Jest to przykład o tyle wygodny, że pojawiają się przy nim nowe pole, przynajmniej dwie nowe metody. W grę wchodzi również wybiórcza modyfikacja metod, które zadeklarował sobie już JAXB. Z drugiej strony przykład ten jest o tyle niezręczny, że jest to już fragment [jaxb-commons](https://jaxb2-commons.dev.java.net/). Jakkolwiek ciężko mi wymyślić coś bardziej kreatywnego co by odnosiło się do ogółu generowanego kodu.

Mamy już pomysł, teraz zatem kolej na kod. Pluginy są podpinane przez XJC i są odpalane po procesie parsowania schematu XML.
Krok po kroku co należy zrobić.

- Stworzyć klasę rozszerzającą **com.sun.tools.xjc.Plugin**
- Stworzyć jar, który będzie zawierał plugin (chociażby skryptem ant). Poniższy przykład pakuje wszystko z bin\_plugin oraz katalog META-INF.

```xml
<project name="plugin" default="jar">
  <target name="jar">
    <delete file="plugin.jar" />
    <jar destfile="plugin.jar">
      <fileset dir="bin_plugin" />
      <fileset file="./*" excludes="*" includes="META-INF/**" />
    </jar>
  </target>
</project>
```

- W paczce (jar) należy należy stworzyć plik **META-INF/services/com.sun.tools.xjc.Plugin**
- W pliku tym należy umieścić listę kolejnych pluginów - to znaczy wymienić pełne nazwy klas, po kolei każdą w nowej linii

```java
com.sun.tools.xjc.addon.locator.SourceLocationAddOn
com.sun.tools.xjc.addon.sync.SynchronizedMethodAddOn
com.sun.tools.xjc.addon.at_generated.PluginImpl
pl.dywicki.plugin.PropertyChangePlugin
```

Mając tak przygotowane archiwum uruchamiamy xjc:
```sh
xjc -cp nasz.jar -extension -nasz\_plugin schemat.xsd
```
Kilka słów wyjaśnienia -cp to class path, -extension włącza pluginy, a -nasz\_plugin informuje xjc, że chcemy uruchomić plugin, który legitymuje się taką dyrektywą.

Po tych wszystkich karkołomnych zabiegach możemy spokojnie zabrać się za kodowanie..
```java
package pl.dywicki.plugin;

import java.beans.PropertyChangeSupport;

import javax.xml.bind.annotation.XmlTransient;

import org.xml.sax.ErrorHandler;

import com.sun.codemodel.JBlock;
import com.sun.codemodel.JDefinedClass;
import com.sun.codemodel.JExpr;
import com.sun.codemodel.JFieldRef;
import com.sun.codemodel.JFieldVar;
import com.sun.codemodel.JInvocation;
import com.sun.codemodel.JMethod;
import com.sun.codemodel.JMod;
import com.sun.codemodel.JVar;
import com.sun.tools.xjc.Options;
import com.sun.tools.xjc.Plugin;
import com.sun.tools.xjc.outline.ClassOutline;
import com.sun.tools.xjc.outline.Outline;

/**
 * Plugin dorzucający do wygenerowanych klas obsługę powiadamiania o zmianach
 *
 * @author Łukasz Dywicki
 */
public class PropertyChangePlugin extends Plugin {

    @Override
    public String getOptionName() {
        // nasz plugin będzie uruchomiony poprzez dyrektywę -Xlistener
        return "Xlistener";
    }

    @Override
    public String getUsage() {
        // help dla użytkownika
        return " -Xlistener propagowanie zdarzeń przy pomocy standardów JavaBeans";
    }

    @Override
    public boolean run(Outline outline, Options opt, ErrorHandler errorHandler) {
        // a to jest najważniejsza część pluginu :)

        // przeglądamy wszystkie dostępne klasy
        for (ClassOutline co : outline.getClasses()) {
            // definicja pojedyńczej klasy, przy pomocy JDefinedClass
            // możemy dodawać nowe własności etc
            JDefinedClass clazz = co.implClass;

            addField(clazz);

            changeMethods(clazz);
        }
        return true;
    }

    /**
     * Dodanie pola do generowanej klasy.
     *
     * @param clazz Klasa do której dorzucamy pole
     */
     private void addField(JDefinedClass clazz) {
         // tworzymy w wygenerowanej klasie pole
         // private PropertyChangeSupport support = new PropertyChangeSupport(this);
         JFieldVar $field = clazz.field(JMod.PRIVATE | JMod.TRANSIENT, PropertyChangeSupport.class, "support");
         // inicjujemy pole
         JInvocation _new = JExpr._new($field.type());
         // dodajemy argument (this)
         _new.arg(JExpr._this());
         $field.init(\_new);
         // dodanie adnotacji @XmlTransient do pola, aby nie było ono
         // obsługiwane przez JAXB
         $field.annotate(XmlTransient.class);
     }

    /**
     * Modyfikacja metod set\*..
     *
     * @param clazz Klasa którą "przerabiamy".
     */
     private void changeMethods(JDefinedClass clazz) {
         for (JMethod method : clazz.methods()) {
             if (method.name().startsWith("set")) {
                 String tmp = method.name().substring(3);
                 String fieldName = tmp.substring(0, 1).toLowerCase() + tmp.substring(1);
                 addSupport(method, fieldName);
             }
         }
     }

    /**
     * Zmodyfikowanie istniejących metod.
     *
     * @param method Obiekt metody
     * @param fieldName Nazwa pola, które metoda obsługuje
     */
     private void addSupport(JMethod method, String fieldName) {
         // przeskakujemy do pierwszej linijki w metodzie
         JBlock body = method.body();
         body.pos(0);
         // odwoładnie do np this.userName
         JFieldRef ref = JExpr.ref(fieldName);
         // deklaracja zmiennej o typie pierwszego argumentu i nazwie temp
         JVar $temp = body.decl(method.listParams()[0].type(), "temp");
         // przypisanie do zmiennej temp wartości pola
         $temp.init(JExpr.ref(fieldName));

         // przeskoczenie na koniec metody
         body.pos(body.getContents().size());

         // odwołanie do pola this.support (stworzone w metodzie addField) i
         // wywołanie metody this.support.firePropertyChange()
         JInvocation invocation = JExpr.ref("support").invoke("firePropertyChange");
         // kolejne argumenty metody - nazwa property, stara wartość i nowa wartość
         invocation.arg(fieldName);
         invocation.arg($temp);
         invocation.arg(ref);

         // dorzucenie wywołania na końcu metody
         body.add(invocation);
     }

}
```

Kosztuje to troszkę pracy, ale koniec końców mamy wsparcie dla obsługi zdarzeń. Dla zainteresowanych dołączam jeszcze dwa pluginy - generujące metody [hashCode](http://media.dywicki.pl/blog/jaxb/HashCodePlugin.java) i [equals](http://media.dywicki.pl/blog/jaxb/EqualsPlugin.java). Napisałem je, gdy nie miałem dostępu do internetu, jeśli chcecie ich używać - zalecam wcześniej wspomniane jaxb-commons.

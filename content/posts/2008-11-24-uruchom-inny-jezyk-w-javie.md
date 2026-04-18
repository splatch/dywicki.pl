---
author: splatch
category:
  - java
  - javascript
  - porady
date: "2008-11-24T06:00:02+00:00"
guid: http://blog.dywicki.pl/?p=236
summary: Do Javy 6.0 zostało dołączone API ( [JSR 223](http://www.jcp.org/en/jsr/detail?id=223)) umożliwiające wywoływanie różnych języków wewnątrz wirtualnej maszyny. Można w ten sposób przesunąć chociażby moment kompilowania kodu na później bądź od razu podpiąć język interpretowany.
title: Uruchom inny język w Javie
url: /2008/11/24/scripting-inside-java/

---
Do Javy 6.0 zostało dołączone API ( [JSR 223](http://www.jcp.org/en/jsr/detail?id=223)) umożliwiające wywoływanie różnych języków wewnątrz wirtualnej maszyny. Można w ten sposób przesunąć chociażby moment kompilowania kodu na później bądź od razu podpiąć język interpretowany.

Poniżej przykład:
```java
package org.code_house.scripting;

import javax.script.ScriptEngine;
import javax.script.ScriptEngineFactory;
import javax.script.ScriptEngineManager;

public class JSMain {

 public static void main(String[] args) throws Exception {
 ScriptEngineManager manager = new ScriptEngineManager();

 System.out.println("Dostępne języki");
 for (ScriptEngineFactory engine : manager.getEngineFactories()) {
 System.out.println(engine.getNames());
 }

 ScriptEngine engine = manager.getEngineByName("js");
 engine.put("y", 12);

 if (engine != null) {
 engine.eval("var x = 1; print(x + y);");
 }
 }

}
```

W taki oto sposób na naszej konsoli powinno wylądować coś takiego:
```
[js, rhino, JavaScript, javascript, ECMAScript, ecmascript]
13
```

![Swing + Java Script](/wp-content/uploads/2008/11/js-swing.jpg)

Możemy również uruchomić plik js. Wystarczy do metody **eval** przekazać FileReader. Po uruchomieniu kodu który jest niżej powinno pokazać się okienko podobne do tego, które widzicie przy tym akapicie.

```java
package org.code_house.scripting;

import java.io.*;
import javax.script.*;

public class JavaScriptMain {
 public static void main(String[] args) throws Exception {
 ScriptEngineManager manager = new ScriptEngineManager();
 ScriptEngine engine = manager.getEngineByName("js");
 engine.eval(new FileReader(new File("print.js")));
 }
}
```
```js
frame = new javax.swing.JFrame;
frame.defaultCloseOperation = javax.swing.JFrame.EXIT_ON_CLOSE;
frame.title = "Test"
frame.setSize(new java.awt.Dimension(180, 80));

button = new javax.swing.JButton("Kliknij mnie")
button.addActionListener(function(event) {
 print(event.source);
});
frame.add(button);

frame.show()
```

Z dodatkowych języków można podpiąć między innymi [PHP](http://php.net), [Java FX](http://java.sun.com/javafx/), [Groovy](http://groovy.codehaus.org), [BeanShell](http://www.beanshell.org/) i wiele innych. Aby to zrobić należy ściągnąć ze strony [scripting.dev.java.net](https://scripting.dev.java.net/) rozszerzenia (dostępne w sekcji Documents & files) i dodać JAR dla języka którego potrzebujemy do classpath.

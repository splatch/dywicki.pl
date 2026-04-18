---
author: splatch
category:
  - wiadomości
date: "2008-11-21T10:56:19+00:00"
draft: "true"
guid: http://blog.dywicki.pl/?p=237
title: Skompiluj i uruchom Javę wewnątrz działającej JVM!
url: /

---
Przykład dla Javy:
```java
import java.io.File;
import java.io.FileReader;
// ... i dalej
ScriptEngine engine = manager.getEngineByName("java");
Object o = engine.eval(new FileReader(new File("Test.java")));
```

Plik który odpalamy,

---
author: splatch
category:
  - agavi
  - db
  - inne
  - java
  - wiadomości
  - xml
date: "2007-11-29T23:21:25+00:00"
guid: http://blog.dywicki.pl/?p=201
title: Agavi IDE, db4o 7.0, ObjectNotStorableException i Ant :)
url: /2007/11/30/agavi-ide-db4o-70-objectnotstorableexception-i-ant/

---
Dnia dzisiejszego zmobilizowałem się by nieco popracować nad modelem dla Agavi IDE. Jest to podstawa bez której ciężko budować kolejne edytory. Trzeba w końcu wiedzieć jakie akcje, widoki etc są w projekcie by sugerować użytkownikowi poprawne wartości.
Jak donosi [Michał Mech](http://michalmech.pl) na [swoim blogu](http://michalmech.pl/index.php/2007/11/29/symfoclipse-wsparcie-dla-symfony-na-platformie-eclipse/), konkurencja nie śpi i wszczęła prace nad integracją Eclipse i Symfony - tak też narodziło się [Symfoclipse](http://). Stąd straciłem już palmę pierwszeństwa i pozostaje mi walczyć na polu jakości i funkcjonalności. [Zend](http://zend.com) również serwuje wsparcie dla [Zend Framework](http://framework.zend.com/), aczkolwiek po [prezentacji](http://www.zend.com/webinar/studio/EN_11_Zend_Studio_Neon_Zend_Framework_Support/) miałem mieszane uczucia. Przede wszystkim lektor mówiący z paskudnym angielskim akcentem. Nie wiem, może jestem wyczulony, ale gdy słyszę te fonetyczne wygibasy to mnie to mierzi, może dla jednych to plus, ale dla mnie lepszym sprzedawcą produktu jest ktoś kto mówi o swoim projekcie chociażby z francuskim akcentem niż wynajęty lektor, który ma pomnożyć wrażenie "powagi" przedsięwzięcia. Na drugim miejscu jakość "wsparcia", bo inaczej jak w cudzysłowach tego nie mogę określić, jest po prostu żenująca. Czy mi się wydaje czy oni każą sobie płacić za dodanie kreatora (wizzarda)? ;) Coś mi się wydaje, że załoga [PHP Eclipse](http://phpeclipse.de/) dobrze robi ponownie zwierając szyki!

Oczywiście przy pisaniu napotkałem problemy. Pierwszym z nich było zniknięcie klasy buildera, drugim wywalanie się db4o (dev 7.0). O ile pierwszy uraczył mnie gromadą logów o tyle drugi poza pokaźnym stosem nie dał mi nic. Typ wyjątku **com.db4o.ext.ObjectNotStorableException** \- nazwa w miarę jasna, problem w tym, że za tym żadnego uzasadnienia, powodu dla którego wyjątek by leciał. Zero, dosłownie zero informacji.
Na forum db4o znalazłem parę postów - z nich wyciągnąłem wniosek - konieczne jest użycie taska ant, który zmodyfikuje bytecode klasy, ponieważ w przeciwnym wypadku db4o próbuje serializować obiekt, a gdy mu się nie uda rzuca w/w wyjątek.

Skrypt ant - który załatwia problem, dla potomnych ( [przeróbka z forum](http://developer.db4o.com/forums/thread/43766.aspx)):
```xml
<project name="db4o enhance project" default="enhance" basedir=".">

  <!-- inny projekt z workspace'a -->
  <property name="model.classes.location" location="E:/agavi-ide/org.codehouse.model" />
  <!--
  położenie eclipse, db4o szpera w poszukiwaniu zależności klas, tak się składa,
  że mój kod korzysta z paru rzeczy z org.eclipse.core.resources, normalnie ta
  wstawka się nie pojawia
  -->
  <property name="eclipse.location" location="E:/eclipse-3.3/eclipse" />

  <target name="pack-model">
    <echo message="Tworzę archiwum z modelem" />
      <jar destfile="lib/model.jar">
          <fileset dir="${model.classes.location}/bin/">
            <!-- włączamy wszystkie skomilowane klasy -->
              <include name="**/*.class" />
          </fileset>
      </jar>
  </target>

  <!-- classpath dla projektu -->
  <path id="project.classpath">
    <pathelement path="${basedir}/bin" />
    <fileset dir="lib">
      <include name="**/*.jar" />
    </fileset>
    <fileset dir="${eclipse.location}/plugins/">
      <include name="**/*.jar" />
    </fileset>
  </path>

    <!-- odwołanie do taska z db4o-tools.jar -->
    <taskdef name="db4o-enhance" classname="com.db4o.enhance.Db4oEnhancerAntTask" classpathref="project.classpath" />

  <target name="enhance" depends="pack-model">
    <!-- przeoranie źródeł i zależności -->
    <db4o -enhance classtargetdir="${basedir}/bin" jartargetdir="${basedir}/lib">
      <classpath refid="project.classpath" />
      <sources dir="${basedir}/bin" /> <!-- w tym katalogu db4o zajmie się plikami .class -->
      <jars dir="${basedir}/lib">
        <include name="*.jar" />
        <exclude name="db4o-*.jar" />
        <exclude name="ant.jar" />
        <exclude name="bloat-1.0.jar" />

      </jars>
    </db4o -enhance>
  </target>

</project>
```

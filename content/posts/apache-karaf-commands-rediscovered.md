---
author: splatch
category:
  - uncategorized
date: "2010-12-15T12:05:49+00:00"
guid: http://blog.code-house.org/?p=308
summary: I work with Apache Karaf almost every day. There is a lot of commands provided by default and most of them are a bit anonymous. In this post I would like introduce these commands.
title: Apache Karaf commands rediscovered
url: /2010/12/apache-karaf-commands-rediscovered/

---
I work with Apache Karaf almost every day. There is a lot of commands provided by default and most of them are a bit anonymous. In this post I would like introduce these commands.

## List bundles

Common command executed in Karaf shell is **list**. There is few switches which makes this command more usable. First of them is **-l** which shows bundle locations, second is **-t**. Second switch is available from Karaf 2.1.

Below example output of these commands:

```

        __ __                  ____
       / //_/____ __________ _/ __/
      / ,<  / __ `/ ___/ __ `/ /_
     / /| |/ /_/ / /  / /_/ / __/
    /_/ |_|__,_/_/   __,_/_/

  Apache Karaf (2.1.2)

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
Hit '<ctrl-d>' or 'osgi:shutdown' to shutdown Karaf.

karaf@root> list -l
START LEVEL 100 , List Threshold: 50
   ID   State         Blueprint      Spring    Level  Location
[  34] [Resolved   ] [            ] [       ] [   60] mvn:org.apache.geronimo.specs/geronimo-servlet_2.5_spec/1.1.2
[  39] [Active     ] [            ] [       ] [   60] mvn:org.apache.servicemix.bundles/org.apache.servicemix.bundles.jetty/6.1.25_1

karaf@root> list -l -t 0
START LEVEL 100 , List Threshold: 0
   ID   State         Blueprint      Spring    Level  Location
[   0] [Active     ] [            ] [       ] [    0] System Bundle
[   1] [Active     ] [            ] [       ] [    5] mvn:org.ops4j.pax.url/pax-url-mvn/1.2.1
[   2] [Active     ] [            ] [       ] [    5] mvn:org.ops4j.pax.url/pax-url-wrap/1.2.1
[   3] [Active     ] [            ] [       ] [    8] mvn:org.ops4j.pax.logging/pax-logging-api/1.5.3
[   4] [Active     ] [            ] [       ] [    8] mvn:org.ops4j.pax.logging/pax-logging-service/1.5.3
[   5] [Active     ] [            ] [       ] [   10] mvn:org.apache.felix/org.apache.felix.configadmin/1.2.4
[   6] [Active     ] [            ] [       ] [   11] mvn:org.apache.felix/org.apache.felix.fileinstall/3.0.2
[   7] [Active     ] [Created     ] [       ] [   20] mvn:org.apache.aries.blueprint/org.apache.aries.blueprint/0.2-incubating
[   8] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.jaas/org.apache.karaf.jaas.config/2.1.2
[   9] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.admin/org.apache.karaf.admin.command/2.1.2
[  10] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf/org.apache.karaf.management/2.1.2
[  11] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.deployer/org.apache.karaf.deployer.spring/2.1.2
[  12] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.features/org.apache.karaf.features.core/2.1.2
[  13] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.packages/2.1.2
[  14] [Active     ] [            ] [       ] [   30] mvn:org.apache.aries.jmx/org.apache.aries.jmx.blueprint/0.2-incubating
[  15] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.jaas/org.apache.karaf.jaas.modules/2.1.2
[  16] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.ssh/2.1.2
[  17] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.features/org.apache.karaf.features.management/2.1.2
[  18] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.features/org.apache.karaf.features.command/2.1.2
[  19] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.log/2.1.2
[  20] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.admin/org.apache.karaf.admin.core/2.1.2
[  21] [Active     ] [            ] [       ] [   30] mvn:org.apache.aries.jmx/org.apache.aries.jmx/0.2-incubating
[  22] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.deployer/org.apache.karaf.deployer.blueprint/2.1.2
[  23] [Active     ] [            ] [       ] [   30] mvn:org.apache.mina/mina-core/2.0.0-RC1
[  24] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.dev/2.1.2
[  25] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.osgi/2.1.2
[  26] [Active     ] [            ] [       ] [   30] mvn:org.apache.sshd/sshd-core/0.4.0
[  27] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.commands/2.1.2
[  28] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.deployer/org.apache.karaf.deployer.features/2.1.2
[  29] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.console/2.1.2
[  30] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.admin/org.apache.karaf.admin.management/2.1.2
[  32] [Active     ] [Created     ] [       ] [   30] mvn:org.apache.karaf.shell/org.apache.karaf.shell.config/2.1.2
[  34] [Resolved   ] [            ] [       ] [   60] mvn:org.apache.geronimo.specs/geronimo-servlet_2.5_spec/1.1.2
[  39] [Active     ] [            ] [       ] [   60] mvn:org.apache.servicemix.bundles/org.apache.servicemix.bundles.jetty/6.1.25_1
```

As you see first comman returns short list which contains bundles installed by me (servlet api and jetty). Second list contains bundles default installed also by Karaf. Another usefull switch is **-s** which shows symbolic names:

```
karaf@root> list -s
START LEVEL 100 , List Threshold: 50
   ID   State         Blueprint      Spring    Level  Symbolic name
[  34] [Resolved   ] [            ] [       ] [   60] org.apache.geronimo.specs.geronimo-servlet_2.5_spec (1.1.2)
[  39] [Active     ] [            ] [       ] [   60] org.apache.servicemix.bundles.jetty (6.1.25.1)
```

Switch -t may be mixed with both -s and -l.

## List services

After first impression with OSGi and bundles as modules most of us moving to using OSGi services. That's really cool stuff and gives a lot of fun, but without helper commands we may stuck. Karaf provides command named **ls** which shows services exported by given bundle.

```
karaf@root> ls 132

Apache Karaf :: Web Console :: Admin Plugin (132) provides:
-----------------------------------------------------------
osgi.service.blueprint.compname = adminPlugin
felix.webconsole.label = admin
objectClass = javax.servlet.Servlet
service.id = 176
----
osgi.blueprint.container.version = 2.1.2
osgi.blueprint.container.symbolicname = org.apache.karaf.webconsole.admin
objectClass = org.osgi.service.blueprint.container.BlueprintContainer
service.id = 178
```

If we would check which services are in use by our bundle we have **very usefull** switch **-u**.

```
karaf@root> ls -u 21
Apache Aries JMX Bundle (21) uses:
----------------------------------
service.vendor = Apache Software Foundation
service.pid = org.apache.felix.cm.ConfigurationAdmin
service.description = Configuration Admin Service Specification 1.2 Implementation
objectClass = org.osgi.service.cm.ConfigurationAdmin
service.id = 33
```

## List packages

When you work under OSGi it's important which packages you import and export. The two commands **packages:imports** and **packages:exports** will simply show what's comes and goes from your bundle. I'll not show how these commands work but I have little trick for you. When you're unable to resolve bundle because you have missing import packages and you have ClassNotFoundException type **dev:dynamic-import bundleid** command. This command add **DynamicImport-Package: \*** entry to bundle manifest. After that, when you'll resolve bundle type packages:imports and check complete list of import you missed in your headers.

## Features

All commands shown above are strictly related to OSGi. But Karaf is a little bigger and allow you do more than OSGi execution environment. One of tools which Karaf build on top of OSGi framework is features mechanism. You may define list of things to install and add dependencies between them instead typing install command line after line. But sometimes you would like to check what given feature contains. To do that type **features:info** command. This command requires feature name as argument.

```
karaf@root> features:info webconsole

Description of webconsole 2.1.2 feature
----------------------------------------------------------------
Feature has no configuration
Feature depends on:
  webconsole-base 2.1.2
Feature contains followed bundles:
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.admin/2.1.2
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.features/2.1.2
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.gogo/2.1.2
```

You may use few additional switches: **-b**, **-d**, **-c**. First shows bundles in feature, second bundle dependencies and last feature configuration. We have also another switch **-t** which shows all these informations plus tree of features and it's bundles.

```
karaf@root> features:info -t webconsole
Description of webconsole 2.1.99-SNAPSHOT feature
----------------------------------------------------------------
Feature has no configuration
Feature depends on:
  webconsole-base 2.1.99-SNAPSHOT
Feature contains followed bundles:
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.admin/2.1.99-SNAPSHOT
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.features/2.1.99-SNAPSHOT
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.gogo/2.1.99-SNAPSHOT

Feature tree
 webconsole 2.1.99-SNAPSHOT
 + mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.admin/2.1.99-SNAPSHOT
 + mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.features/2.1.99-SNAPSHOT
  mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.gogo/2.1.99-SNAPSHOT
    webconsole-base 2.1.99-SNAPSHOT
    + mvn:org.apache.felix/org.apache.felix.metatype/1.0.4
    + mvn:org.apache.karaf.webconsole/org.apache.karaf.webconsole.branding/2.1.99-SNAPSHOT
     mvn:org.apache.felix/org.apache.felix.webconsole/3.1.6
       http 2.1.99-SNAPSHOT
       + mvn:org.ops4j.pax.web/pax-web-api/0.8.1
       + mvn:org.ops4j.pax.web/pax-web-spi/0.8.1
       + mvn:org.ops4j.pax.web/pax-web-runtime/0.8.1
        mvn:org.ops4j.pax.web/pax-web-jetty/0.8.1
          jetty [7.0,8.0) *
Tree contains 1 unresolved dependencies
 * means that node declares dependency but the dependant feature is not available.
```

## Development commands

I mentioned dev:dynamic-import command before. But Karaf have few more commands which makes development easier. First of all is **dev:show-tree** which shows bundles tree, for example:

```
karaf@root> dev:show-tree 39
Bundle dump [39] is currently ACTIVE

dump [39]
+- org.apache.karaf.diagnostic.core [16]
+- org.apache.aries.blueprint [7]
   +- org.apache.felix.configadmin [5]
   |  +- org.ops4j.pax.logging.pax-logging-api [3]
   +- org.ops4j.pax.logging.pax-logging-api [3]
```

Another command you may use is **dev:framework** which allows you change OSGi framework used by Karaf. I don't use this command to often.

Last command I would introduce is **dev:create-dump** commited to Karaf trunk by me. This command creates zip archive which contains diagnostic stuff you may attach to JIRA or send to developers in your company to check what was wrong. By default dumps contains log entries from $KARAF\_BASE/data/log, list of installed bundles and features. You may also create new diagnostic providers. Sample code is available in SVN: [demo](https://svn.apache.org/repos/asf/karaf/trunk/demos/dump/), [provider class](https://svn.apache.org/repos/asf/karaf/trunk/demos/dump/src/main/java/org/apache/karaf/diagnostic/demo/ScreenshotDumpProvider.java), [blueprint config](https://svn.apache.org/repos/asf/karaf/trunk/demos/dump/src/main/resources/OSGI-INF/blueprint/screenshot.xml).

## Complete

Remember that every Karaf command can be executed with **--help** switch which shows all arguments and switches. In this post you was introduced to following commands:

- list and switches -t -l -s
- ls and -u switch
- packages:imports, packages:exports, dev:dynamic-import
- features:info and switches -b -d -c and -t
- dev:show-tree, dev:framework, dev:create-dump

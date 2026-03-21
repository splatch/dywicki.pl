---
author: splatch
category:
  - osgi
date: "2011-11-24T19:23:46+00:00"
guid: http://dywicki.pl/?p=707
summary: One of bigest benefits of Java is byte code manipulation. You can change everything you want in your application without touching source code. That's usefull for many cases, starting from legacy code, where we can't simply modify and recompile library up to modern applications where aspects can be used to handle runtime exceptions. The most popular project is [AspectJ](http://eclipse.org/aspectj/) which is part of [Eclipse](http://eclipse.org) ecosystem, in this post I going to show you how to use AspectJ with Karaf.
tag:
  - aspectj
  - karaf
title: Running aspects under OSGi 4.2 with Karaf
url: /2011/11/running-aspects-under-osgi-4-2-with-karaf/

---
One of bigest benefits of Java is byte code manipulation. You can change everything you want in your application without touching source code. That's usefull for many cases, starting from legacy code, where we can't simply modify and recompile library up to modern applications where aspects can be used to handle runtime exceptions. The most popular project is [AspectJ](http://eclipse.org/aspectj/) which is part of [Eclipse](http://eclipse.org) ecosystem, in this post I going to show you how to use AspectJ with Karaf.

## Enabling AspectJ

Byte code manipulation requires access to class resource (.class file). It can be obtained from class loader which was used to load the class. Under Tomcat it's easy. Anywhere you are, you can use MyClass.class.getClassLoader() and you will access web application class loader. But how to handle it under OSGi, where every bundle have own class loader? Equinox supports technic called hooks. It allows us, the developers, to extend framework in more declarative way. Hooks became very useful and since OSGi 4.3 release all certified frameworks will support few:

- Resolver Hook
- Bundle Hook
- Service Hook
- and finally Weaving Hook

[![Setting equinox as default framework in Karaf](/wp-content/uploads/2011/11/equinox-vim-300x172.png)](/wp-content/uploads/2011/11/equinox-vim.png)
The core difference between hooks in Equinox and in OSGi 4.3 is how they are registered. OSGi 4.3 allows to register hooks as typical services. In older Equinox they're loaded from framework configuration or they are discovered by classpath scanning (they must be attached to Eqinox bundle as fragments). So whole trick is to start framework with hooks JARs. Also, to make aspects works, in older version, AspectJ must be loaded before any woven class, but that's logical. You can't weave classes when they already loaded and cached by JVM.

## Enabling in Karaf

Because actual version of Karaf (2.2.4) still works with OSGi 4.2 we'll use older way to enable aspects. First of all, before launch we'll change default framework. By default Karaf distribution uses Felix as OSGi framework. Open the **etc/config.properties** and change the **karaf.framework** property to **equinox**.
\[java\]
karaf.framework=equinox
\[/java\]

If you are interested in different versions of same framework, you can simply hack this file because the karaf.framework is used to look up another property. Few lines below in configuration file you can see:
\[plain\]
karaf.framework.equinox=${karaf.default.repository}/org/eclipse/osgi/3.6.2.R36x\_v20110210/osgi-3.6.2.R36x\_v20110210.jar
karaf.framework.felix=${karaf.default.repository}/org/apache/felix/org.apache.felix.framework/3.0.9/org.apache.felix.framework-3.0.9.jar
\[/plain\]

Framework JAR is located by expression **karaf.framework.${karaf.framework}**. But that's only a note for curious people. ;)

[![Files in Karaf home and lib](/wp-content/uploads/2011/11/dir-struct-300x204.png)](/wp-content/uploads/2011/11/dir-struct.png) Once we switched to Equinox we have only few steps left. First of all we need to install aspectj and weaving hook. Last part is example aspects. I had problem to locate fresh version of Weaving hook because project was moved from Equinox Incubator to Equinox Bundles. We need download whole [Equinox SDK](http://download.eclipse.org/equinox/) to grab following artifacts from plugins directory:

- org.eclipse.equinox.weaving.aspectj\_1.0.1.v20110502.jar
- org.eclipse.equinox.weaving.caching\_1.0.100.v20110502.jar
- org.eclipse.equinox.weaving.hook\_1.0.100.v20110502.jar

I copied these artifacts to karaf home directory except **org.eclipse.equinox.weaving.hook**. It goes to lib directory, otherwise hook won't be discovered. That's because hook discovery is done by property file lookup. The lookup uses framework class loader and in Karaf case it is URLClassLoader with lib/ entries and framework archive.

In addition we'll need also AspectJ runtime. We'll install it from [ServiceMix](http://servicemix.apache.org) bundles [repository](http://repo1.maven.org/maven2/org/apache/servicemix/bundles/) but it will be installed directly from maven. No additional download steep need. After that we can launch bin/karaf and start installing artifacts:

```
~/tools/apache-karaf-2.2.4-switch$ ./bin/karaf

        __ __                  ____
       / //_/____ __________ _/ __/
      / ,<  / __ `/ ___/ __ `/ /_
     / /| |/ /_/ / /  / /_/ / __/
    /_/ |_|\__,_/_/   \__,_/_/

  Apache Karaf (2.2.4)

Hit '<tab>' for a list of available commands
and '[cmd] --help' for help on a specific command.
karaf@root> install file:lib/org.eclipse.equinox.weaving.hook_1.0.100.v20110502.jar
Bundle ID: 49
karaf@root> install file:org.eclipse.equinox.weaving.aspectj_1.0.1.v20110502.jar
Bundle ID: 50
karaf@root> install file:org.eclipse.equinox.weaving.caching_1.0.100.v20110502.jar
Bundle ID: 51
karaf@root> install mvn:org.apache.servicemix.bundles/org.apache.servicemix.bundles.aspectj/1.6.8._2
Bundle ID: 52
karaf@root> list
[  49] [Resolved   ] [            ] [   60] Aspect Weaving Hooks Plug-in (Incubation) (1.0.100.v20110502)
                                       Hosts: 0
[  50] [Active     ] [            ] [   60] WeavingService Plug-in (Incubation) (1.0.1.v20110502)
[  51] [Active     ] [            ] [   60] Standard Caching Service for Equinox Aspects (Incubation) (1.0.100.v20110502)
[  52] [Active     ] [            ] [   60] Apache ServiceMix :: Bundles :: aspectj (1.6.8.2)

```

To resolve correctly fragment bundle and connect it with equinox system bundle an restart may be needed. I recommend to restart, because it is the best way to connect everything together. Optionally you may set start level to be lower than default - 60. But for test purposes it may be leaved as is.

## Running aspects

After installing all stuff it would be nice to run some aspects, isn't? :) If we have environment ready to handle byte code manipulation it would be nice to use it. As an example Aspect I will use char counter. After writing to PrintStream instance it will write information how many characters was writen and to which stream. I don't know AspectJ syntax so it might not be optimal - feedback is welcome.
\[java\]
package org.code\_house.workshop.karaf.aspect.aspectj;

import java.io.PrintStream;
import org.osgi.framework.BundleActivator;
import org.osgi.framework.BundleContext;;

public aspect HelloAspect {

 after(String msg, PrintStream stream) : // after call
 call(void PrintStream.println(String)) // println
 && !within(HelloAspect) // not from Aspect
 && args(msg) // with String attribute
 && target(stream) { // and PrintStream as a target
 stream.println("You just wrote " + msg.length() + " chars to " + stream); // display message
 }
}
\[/java\]
Because we going to weave classes during load time we need **META-INF/aop.xml** file to let discover our aspects. Contents of file are really simple and points to aspect class name. Also it worth to notify to be careful with AspectJ version. I've got an error:
\[plain\]BCException: Unable to continue, this version of AspectJ supports classes built with weaver version 6.0 but the class org.code\_house.workshop.karaf.aspect.aspectj.HelloAspect is version 7.0\[/plain\]
The root cause is version mismatch between runtime and compile time. I used version 1.6.11 to compiler and 1.6.8 to run. After aligning versions to 1.6.8 it started working again.
Second, important thing is a specific manifest entry **Eclipse-SupplementBundle**. It says which bundle is enhanced by aspects contained inside declaring bundle. I've put **org.code-house.workshop.karaf.aspect.bundle** because I going only to instrument one bundle. But value of this header might be an wildcard. Second option is to add **Require-Bundle** header pointing to org.aspectj.runtime.

So the last step is to install our artifacts and check if they're runnign correctly. Let's do that.

```
karaf@root> install mvn:org.code-house.workshop.karaf.aspect/aspectj/1.0.0.SNAPSHOT
Bundle ID: 53
karaf@root> install mvn:org.code-house.workshop.karaf.aspect/bundle/1.0.0.SNAPSHOT
Bundle ID: 54
karaf@root>  list
[  53] [Active     ] [            ] [   60] Code-House :: Workshop :: Karaf :: Aspect :: AspectJ (1.0.0.SNAPSHOT)
[  54] [Active     ] [            ] [   60] Code-House :: Workshop :: Karaf :: Aspect :: Bundle (1.0.0.SNAPSHOT)

karaf@root> start 53 54
Starting org.code-house.workshop.karaf.aspect.bundle
You just wrote 52 chars to org.apache.felix.gogo.runtime.threadio.ThreadPrintStream@327800e9

```

[![Aspect output in console](/wp-content/uploads/2011/11/karaf-output-150x150.png)](/wp-content/uploads/2011/11/karaf-output.png)

## Summary

Whole code used to write this post is [publically available](https://github.com/splatch/example-karaf-aspectj) on [GitHub](http://github.com). Remember to use Aspects carefully, because they might change behaviour on whole environment, especially when you use centralized load time weaving. It's worth to add excludes in your aop.xml file to don't w enhance packages different than we really wish to instrument.
If you are interested in AspectJ usage with OSGi 4.3, you can follow [this topic](http://dev.eclipse.org/mhonarc/lists/equinox-dev/msg06822.html).

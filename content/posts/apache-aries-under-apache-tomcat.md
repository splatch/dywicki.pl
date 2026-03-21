---
author: splatch
category:
  - osgi
date: "2012-01-24T20:51:40+00:00"
guid: http://dywicki.pl/?p=754
summary: Few months ago I've read an article written by my friend [Jacek Laskowski](http://jlaskowski.blogspot.com) \- [Enterprise OSGi runtime setup with Apache Aries Blueprint](http://www.jaceklaskowski.pl/wiki/Enterprise_OSGi_runtime_setup_with_Apache_Aries_Blueprint "Enterprise OSGi runtime setup with Apache Aries Blueprint"). In his article Jacek describes which bundles should be installed to get the blueprint working. As IBM employee Jacek can always promote IBM WebSphere in version X or Y which started (or will start) supporting blueprint as dependency injection mechanism. That's not fine for these who do not run IBM producs and want something light. As you know, Aries and OSGi Blueprint is an alternative for old-fashion Spring approach.
title: Apache Aries under Apache Tomcat
url: /2012/01/apache-aries-under-apache-tomcat/

---
Few months ago I've read an article written by my friend [Jacek Laskowski](http://jlaskowski.blogspot.com) \- [Enterprise OSGi runtime setup with Apache Aries Blueprint](http://www.jaceklaskowski.pl/wiki/Enterprise_OSGi_runtime_setup_with_Apache_Aries_Blueprint "Enterprise OSGi runtime setup with Apache Aries Blueprint"). In his article Jacek describes which bundles should be installed to get the blueprint working. As IBM employee Jacek can always promote IBM WebSphere in version X or Y which started (or will start) supporting blueprint as dependency injection mechanism. That's not fine for these who do not run IBM producs and want something light. As you know, Aries and OSGi Blueprint is an alternative for old-fashion Spring approach.

## Why I would use Aries Blueprint instead of Spring?

That's good question. Let me answer. First of all Aries runs fine with OSGi. With OSGi you have full modularity and extensive life cycle for your deployment units. Spring Framework is cool and offer you only life cycle for your beans. Just imagine that you don't have to assembly whole webapp every time when your DAO implementation is changed. Also Spring have some issues with custom namespaces and supports only one way of hadling it - fail fast. If you are interested in details you can finds some answers on [Guillaume Nodet](http://gnodet.blogspot.com) blog in [post about custom namespace handlers](http://gnodet.blogspot.com/2010/03/spring-dm-aries-blueprint-and-custom.html).
Of course you can still run Spring-DM under OSGi or select some Spring Source product. For me, blueprint fits OSGi much better.

## How to run OSGi under Tomcat

As you (may) know OSGi is an module layer. Tomcat is an servlet container. If you wish run something under Tomcat that must be an WAR. So to start using OSGi under Tomcat I'll simply embed it inside an war. Because I would expose a servlets registered under OSGi to be accessible throught tomcat connector I'll also use additional proxy service. So there are two different solutions one is Equinox Servlet Bridge and Felix Http Bridge. Both works fine. For this post I've selected Felix artifacts.

Starting Framework is really simple, you just need an instance of ServletContextListener which will start the framework.
```java
public class StartupListener implements ServletContextListener {

    private Logger logger = LoggerFactory.getLogger(StartupListener.class);

    private FrameworkService service;

    public void contextInitialized(ServletContextEvent event) {
        logger.info("Starting felix framework");

        this.service = new FrameworkService(event.getServletContext());
        this.service.start();
    }

    public void contextDestroyed(ServletContextEvent event) {
        logger.info("Framework shutdown");

        this.service.stop();
    }
}
```
I could finish my post here, but I've meet multiple problems. I will describe them here to not lost them in future. :)

### FrameworkService and ProvisioningActivator

First of all, I would like to install some bundles after framework startup. Starting only Felix is not fun at all. We need modules! To get this part done we need to use an specific Felix configuration option called **felix.systembundle.activators**. It allows to pass BundleActivator instances using different classloader/different context than OSGi framework execution. By this way we can simply access resources embeded in WAR. I know it is not really nice solution, but I wanted to keep my demo simple. Just see the ProvisioningActivator code below. We use our servlet context during bundle activation!

```java
public class ProvisionActivator implements BundleActivator {

    private final ServletContext servletContext;

    private static transient Logger logger = Logger.getLogger(ProvisionActivator.class.getName());

    public ProvisionActivator(ServletContext servletContext) {
        this.servletContext = servletContext;
    }

    public void start(BundleContext context) throws Exception {
        servletContext.setAttribute(BundleContext.class.getName(), context);

        List<Bundle> installed = new ArrayList<Bundle>();
        for (URL url : findBundles()) {
            Bundle bundle = context.installBundle(url.getFile(), url.openStream());
            installed.add(bundle);
        }

        for (Bundle bundle : installed) {
            try {
                bundle.start();
            } catch (Exception e) {
                logger.warning("Unable to start bundle " + bundle.getSymbolicName() + ". " + e);
            }
        }
    }

    public void stop(BundleContext context) throws Exception {
    }

    private List<URL> findBundles() throws Exception {
        List<URL> list = new ArrayList<URL>();

        Set paths = this.servletContext.getResourcePaths("/WEB-INF/bundles/");
        logger.info("List of entries in /WEB-INF/bundles/ " + paths);
        for (Object o : paths) {
            String name = (String)o;
            if (name.endsWith(".jar")) {
                URL url = this.servletContext.getResource(name);
                if (url != null) {
                    list.add(url);
                }
            }
        }

        return list;
    }
}
```

Now we need proxy servlet and proxy startup listener. First receives calls from browser and forward its to OSGi servlets, second delivers some servlet events from war to OSGi. The setup is really simple:
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://java.sun.com/xml/ns/javaee
        http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    version="2.5">

    <display-name>Aries WebApp</display-name>

    <listener>
        <listener-class>org.code_house.workshop.aries.war.StartupListener</listener-class><!-- This part starts the Framework -->
    </listener>
    
    <listener>
        <listener-class>org.apache.felix.http.proxy.ProxyListener</listener-class>
    </listener>

    <servlet>
        <servlet-name>proxy</servlet-name>
        <servlet-class>org.apache.felix.http.proxy.ProxyServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>proxy</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>

</web-app>
```

Ok, having the framework running is great, but now we would like to use Felix WebConsole to inspect environment we run. For that we need another important improvement. If you have done some webapps you know that servlet api should not be included in WARs. We need respect same rule in this case. We can not install servlet api as OSGi bundle because ProxyServlet uses WAR classloader, it is not part of any bundle. Because of that we need configure framework to load javax.servlet and javax.servlet.http packages from parent classloader. Just remember to add them in **org.osgi.framework.system.packages** configuration property.
From OSGi point of view we need an bridge which registers HttpService. Bot OSGi part and proxy part are linked. To get everything running you need both of them.

Now we are close to run everything. What we need to remember? Well under OSGi we have to handle dependencies just like under Tomcat, so I'll install following artifacts:

- org.apache.aries.blueprint - blueprint-api, blueprint-core, to support declarative services
- org.apache.aries.proxy - proxy-api, proxy-impl, these two are blueprint dependencies
- org.apache.felix - http bridge, to link OSGi servlets with Proxy Servlet
- org.ops4j.pax.logging - pax-logging-api, pax-logging-service, just to see what happens
- org.apache.felix - webconsole, to track environment

The basic setup can be really tiny. One thing I did not solve is proper logging. Because pax-logging-service uses log4j and some containers also do there is small conflict between those two. I did not investigate this realy deeply, but I suppose that there is need to put them into system packages list.

## Summary

Running OSGi under Tomcat is not hard part. Actually many of providers do that, for example Atlassian have own extension system build on top of OSGi. If your product need an extreme extensibility, then this post is for you. If you will build it correctly then updates will work right after the update without need to restart whole web application.

I hope you enjoyed this part. If you are looking for source code - visit [github](https://github.com/splatch/example-aries-tomcat). The code contains few modules. After build you can drop a war/target/webapp-1.0.0.SNAPSHOT.war into tomcat webapps directory. You can run **mvn jetty:run-war** to see same effect. After that you will be able to access Felix WebConsole at http://localhost:8080/webapp-1.0.0.SNAPSHOT/system/console. The default user and password is admin. Have fun!

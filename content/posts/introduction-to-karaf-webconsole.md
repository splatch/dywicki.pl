---
author: splatch
category:
  - osgi
cover:
  alt: menu entry with icon
  image: /wp-content/uploads/2011/11/menu-entry-with-icon.png
date: "2011-11-16T23:02:22+00:00"
guid: http://dywicki.pl/?p=682
title: Introduction to Karaf WebConsole
url: /2011/11/introduction-to-karaf-webconsole/

---
Management of OSGi - let's face it - is not very hard. The OSGi environment is clearly defined and that gives programmers many mechanisms to create administrative tools. The problem begins when we would like to use only one tool to manage few projects or artifacts of different types. I know this from personal experience because when I run Camel, ActiveMQ and CXF every from them provides own administration console. Every of them requires own security configuration, looks differently, have own dependencies and so on.

This stands a little bit in contradiction with the OSGi specification idea, which tries to unify management of different things, not only dependencies (Core) but aspects like configuration (ConfigAdmin), deployment (DeploymentAdmin), meta data (MetaType), preferences (PreferencesService), users (UserAdmin) or permissions (PermissionAdmin). Naturally, creation of standard for management tools is too hard to be closed in any specification, even so good like OSGi.

## Felix WebConsole

As response for problems with lack of common tool, available from browser a project [Felix WebConsole](http://felix.apache.org/site/apache-felix-web-console.html) was created. This is a subproject of [Felix'a](http://felix.apache.org). In its assumptions Felix WebConsole should let easily extend itself through the use of mechanisms known from the core OSGi - that is, services. It should also let change look and feel and localize the tool. All these assumptions was covered, but number of extensions for Felix WebConsole do not grow like mushrooms after the rain.
The question is, why? Now, in its simplicity Felix WebConsole make difficult creation of more complex extensions such as JMX. The problem is that our extension is only a servlet. From one hand it's too much to put a link in list of bundles, on the other hand it is too little to make a few pages. If we'll try we'll begin to implement the second Struts framework based on servlets.
Project team do not make things easier because it puts on the "lightness" console. It is expected to deploy console on mobile devices too.

## Karaf WebConsol as alternative

Apache Karaf WebConsole is brand new project, which was made few months ago. After dynamic incubation phase, which maybe was too short, it was moved to sub-project of Karaf. Similar as precursor it points to lightness but also points to collaboration with other web frameworks, in this case it is another ASF project - the Apache Wicket. Through its use we obtained far-reaching component model. It means that you may add link to menu and style it with fragment CSS (without fragment bundle). You may add new tab with content or simply put another widget to dashboard. All these things you may see right after logging into console.
All these extension won't be possible without Wicket and Pax-Wicket. Thanks to huge amount of work made by [Andreas Pieber](http://github.com/anpieber) on second project. Everything is stable and works as fast as Felix WebConsole.

## Architecture of Karaf WebConsole

As I meintoned before, WebConsole uses Pax-Wicket and Wicket as presentation layer (we don't count Java Script libraries). Most of extensions uses also blueprint to register services. There is no problem, similary like in case of Felix WebConsole, to use Spring DM or declarative services. Everything works with Pax-Web, but it should be possible to run it with any HttpService.
Whats's more, an experimental branch of pax-wicket which I worked on previously named [jsr330](https://github.com/splatch/pax-wicket-jsr330) let to use same components in a traditional container like is Tomcat. This means that the administrative tool went beyond the OSGi framework and will allow creation of multi-modular consoles, also in a traditional environment.

## Extensions

New elements who are added to Karaf WebConsole are typically Wicket components or they are converted to them. Let see how to add new element to navigation:
\[java\]
package org.apache.karaf.webconsole.blueprint.internal.navigation;

import java.util.ArrayList;
import java.util.List;

import org.apache.karaf.webconsole.blueprint.internal.BlueprintPage;
import org.apache.karaf.webconsole.core.navigation.NavigationProvider;
import org.apache.wicket.Page;
import org.apache.wicket.markup.html.basic.Label;
import org.apache.wicket.markup.html.link.BookmarkablePageLink;
import org.apache.wicket.markup.html.link.Link;

public class BlueprintNavigationProvider implements NavigationProvider {

 public List<Link<Page>> getItems(String componentId, String labelId) {
 List<Link<Page>> items = new ArrayList<Link<Page>>();

 Link<Page> link = new BookmarkablePageLink<Page>(componentId, BlueprintPage.class);
 link.add(new Label(labelId, "Blueprint"));
 items.add(link);

 return items;
 }

}
\[/java\]

![](/wp-content/uploads/2011/11/menu-entry.png)
Interface **org.apache.karaf.webconsole.core.navigation.NavigationProvider** is universal supplier of navigation elements. In web application it's mostly about links. Now, when we have implementation we need to submit it into registry to let use it. In this particular example we going to use blueprint, but it might be a standard activator or any other declarative way as well.
\[xml\]<?xml version="1.0" encoding="utf-8" ?>
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0">

 <service ref="provider" interface="org.apache.karaf.webconsole.core.navigation.NavigationProvider">
 <service-properties>
 <entry key="extends" value="osgi" />
 </service-properties>

 </service>

 <bean id="provider" class="org.apache.karaf.webconsole.blueprint.internal.navigation.BlueprintNavigationProvider" />

</blueprint>
\[/xml\]

Fragment above will cause addition of **BlueprintPage** as child of OSGi menu, because we set extends property. Result of execution you may see on attached picture.

Navigation is only example, remember - you have much more possibilities:

- org.apache.karaf.webconsole.core.brand.BrandProvider - lets to change design (without fragment bundles)
- org.apache.karaf.webconsole.core.navigation.ConsoleTabProvider - causes addition of new tab in navigation
- org.apache.karaf.webconsole.core.navigation.SidebarProvider - adds new elements on left side
- org.apache.karaf.webconsole.core.widget.WidgetProvider - lets to publish new panels with content
- org.apache.karaf.webconsole.osgi.bundle.IActionProvider - adds specific link to bundle list
- org.apache.karaf.webconsole.osgi.bundle.IColumnProvider - adds column to bundle list
- org.apache.karaf.webconsole.osgi.bundle.IDecorationProvider - adds icon bundle list

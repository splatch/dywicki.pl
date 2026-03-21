---
author: splatch
category:
  - middleware
cover:
  alt: mom_producer
  image: /wp-content/uploads/2011/06/mom_producer.png
date: "2011-06-18T12:40:30+00:00"
guid: http://dywicki.pl/?p=633
title: Building sample middleware - producer
url: /2011/06/building-sample-middleware-producer/

---
Last two days I've spent hacking Swing code. I decided to run standalone producer application to show real interaction with broker. You may treat this Swing app like entry point for people to our middleware system. Users simply do "transfers" from this application and don't know anything about technical details. I added text area to main window to show structure of message sent to broker.

## Producing messages with JMS

Most of communication systems, whanever you will go have two different kinds of values, first - main and mainly used is body, second is typical metadata named headers or properties or parameters. JMS is not different, you can create different kinds of messages and set headers to them (in JMS world they're named property, but I preffer header).
Let check messaging code we have:
```java
// import declarations ...
public class JmsMessageSender implements MessageSender, InitializingBean {

 private ConnectionFactory connectionFactory;
 private Session session;
 private final String destination;
 private MessageProducer producer;

 public JmsMessageSender(String destination) {
 this.destination = destination;
 }

 public void sendMessage(String message, Map<String, Object> headers)
 throws Exception {

 if (session == null || producer == null) {
 afterPropertiesSet();
 }

 TextMessage jmsMsg = session.createTextMessage(message);
 for (String header : headers.keySet()) {
 jmsMsg.setObjectProperty(header, headers.get(header));
 }
 producer.send(jmsMsg);
 }

 public void afterPropertiesSet() throws Exception {
 Connection connection = connectionFactory.createConnection();
 session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
 Queue queue = session.createQueue(destination);
 producer = session.createProducer(queue);
 }

 // not important
}
```
First of all we inject connectionFactory using Spring configuration file. In line 20 we create text message, because we sending messages with String as content. Line 22 sets headers for message and finally sends it in line 24 using message producer created in lines 28-31. If we'll look closer these lines we'll see standard initalization code automatically called during bean creation by Spring. In line 29 we create session without transaction support and create producer to send messages. Connection factory is configured in XML with properties file to extract informations like broker url username and password.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

 <bean id="view" class="org.code_house.mom.producer.ProducerWindow">
 <property name="title" value="Bank Agent APP" />
 </bean>

 <bean id="controller" class="org.code_house.mom.producer.ProducerController">
 <constructor-arg ref="view" />
 <property name="messageSender">
 <bean class="org.code_house.mom.producer.JmsMessageSender">
 <constructor-arg value="MOM.Incoming" />
 <property name="connectionFactory" ref="connectionFactory" />
 </bean>
 </property>
 <property name="mapper" ref="mapper" />
 </bean>

 <bean id="connectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
 <property name="brokerURL" value="${url}" />
 <property name="userName" value="${user}" />
 <property name="password" value="${pass}" />
 </bean>

 <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
 <property name="location" value="classpath:activemq.properties" />
 </bean>

 <bean id="mapper" class="org.codehaus.jackson.map.ObjectMapper" />

</beans>
```
I decided to use JSON over XML. Regarding last post about data structures it is not the best choice but it is very simple. So don't treat it as reference implementation. I just wish to don't use JAXB annotations. :-)

### Headers

Headers are common thing for routing. This is main reason why I allow to provide headers as parameter to sendMessage method. In many cases it is better to use headers or even extract headers from body of message in one endpoint to reduce number of content reads. Example how to use headers for routing we'll see in next part about [Apache Camel](http://camel.apache.org).

Just as a note types of headers supported by JMS spec:
[![](/wp-content/uploads/2011/06/mom_producer-300x300.png)](/wp-content/uploads/2011/06/mom_producer.png)

- Boolean
- Byte
- Short
- Int
- Long
- Float
- Double
- String
- Object

We can get or set headers using get **Type** Property(String name) or set **Type** Property(String name, Type value). Remember that headers of type "object" set by setObjectProperty must be serializable, otherwise they will be dropped before sending.

## Producer application

As I said at begining of the post I spent two days on Swing hacks. :) I injected two additional depentencies to this module - [mig layout](www.miglayout.com) and [better beans binding](http://betterbeansbinding.kenai.com/). First is responsible for frame layout, second for interactions between model and Swing controls. Whole application is simple form. All informations are displayed in status bar at bottom of the window, rest is taken by message area and combo boxes.

I have say, that binding framework is cool and reduced number of code lines I had to write without it. If you are interested in exact code structure, please go to [producer module](https://github.com/splatch/mom-sample/tree/master/producer) in [mom-sample github repository](https://github.com/splatch/mom-sample). Because it is not in area of this post I will not write more about desktop implementation details.

For these who wish run sample (I belive you would do that) - after executing
```bash
mvn clean install
```
You can simply execute target/producer.jar by doulbe click. This is fat-jar with all dependencies needed by producer application.

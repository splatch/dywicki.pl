---
author: splatch
category:
  - middleware
date: "2011-06-10T16:45:30+00:00"
guid: http://dywicki.pl/?p=560
summary: |-
  At end of the May I had great time in United Kingdom providing consulting. After this I started thinking about sharing an idea of middleware application based on ActiveMQ and Camel features. I've spent few days to create sufficient example.

  # What middleware is?

  Middleware is a general term, about some software which is some kind of proxy between other systems. What for? - you could ask. Generally because communication from point to point is not the best to build bigger applications, and some components in middle of communication allows us to inject new logic without changing source systems. Could you imagine situation where you have a number of system producing messages and an number of consumers of these messages written in different languages? That's typical case where middleware is going to be usefull.
  There is number of middleware tools from various categories, in this post we'll learn how to use ActiveMQ to build message oriented middleware. What does the MOM means? Generally that we have asynchronous communication without direct method invocations. Producer don't know anything about consumer and vice versa. If you are interested in MOM - pick up " [ActiveMQ in Action](http://manning.com/snyder/)" book (written by commiters of ActiveMQ) from [Manning Publications Co.](http://manning.com) where this term is described in greater detail.
title: Building sample middleware - broker
url: /2011/06/building-sample-middleware-broker/

---
At end of the May I had great time in United Kingdom providing consulting. After this I started thinking about sharing an idea of middleware application based on ActiveMQ and Camel features. I've spent few days to create sufficient example.

# What middleware is?

Middleware is a general term, about some software which is some kind of proxy between other systems. What for? - you could ask. Generally because communication from point to point is not the best to build bigger applications, and some components in middle of communication allows us to inject new logic without changing source systems. Could you imagine situation where you have a number of system producing messages and an number of consumers of these messages written in different languages? That's typical case where middleware is going to be usefull.
There is number of middleware tools from various categories, in this post we'll learn how to use ActiveMQ to build message oriented middleware. What does the MOM means? Generally that we have asynchronous communication without direct method invocations. Producer don't know anything about consumer and vice versa. If you are interested in MOM - pick up " [ActiveMQ in Action](http://manning.com/snyder/)" book (written by commiters of ActiveMQ) from [Manning Publications Co.](http://manning.com) where this term is described in greater detail.

## The scenario

In our scenario we we'll use number of queues, every client will have own queue and will consume messages only from this queue. The producer will send messages with header which will be used to determine client queue. In the middle we have [Apache Camel](http://camel.apache.org) which will process incoming messages and dispatch them to concrete client queues. Camel will also copy messages to log queue for audit processor (not listed on image).
[![](/wp-content/uploads/2011/06/mom_architecture.png)](/wp-content/uploads/2011/06/mom_architecture.png)
As alternative I going to show you how to extract client ID from message body instead of forcing producer to use headers.

## Setting up ActiveMQ

If you are Linux or MacOS user you could use [brew](https://github.com/mxcl/homebrew) package manager to install activemq using following command:
```bash
brew install activemq
```
Other users can download ActiveMQ package from [project site](http://activemq.apache.org/).

Ok, we have ActiveMQ installed, in next steep we going to create an test instance of activemq where we'll hack configuration. To do this execute following command:
```bash
activemq-admin create mom
```
After execution of command you should get following directory structure
[![](/wp-content/uploads/2011/06/mom_broker.png)](/wp-content/uploads/2011/06/mom_broker.png)

We don't going to use all of these files, so in my set up I going to remove next configuration files:

- Because we don't going to use SSL yet:
  - broker.ks
  - broker.ts
- Because we don't use security yet:
  - credentials.properties
- Because we don't going to use ActiveMQ webconsole:
  - jetty-realm.properties
  - jetty.xml

These files are unecessary at this moment in our setup.

## Configuring ActiveMQ

Becase activemq-admin script creates dummy broker configuration we have to modify it a bit. **Remember to put elements in XML file with alphabetic order. Since ActiveMQ 5.4 release all elements are ordered.**. First of all we going to modify destinationPolicy element:
```xml
 <destinationPolicy>
 <policyMap>
 <policyEntries>
 <policyEntry queue=">" optimizedDispatch="true" lazyDispatch="false" producerFlowControl="false" memoryLimit="12 mb" />
 <policyEntry queue="MOM.Client.>" advisoryForDelivery="true" advisoryForConsumed="true" />
 </policyEntries>
 </policyMap>
 </destinationPolicy>
```
First policyEntry element disables producerFlowControl. In other words ActiveMQ will accept all messages sent by producer, even when the memory limit was exceeded. Messages that are not able to be put in memory will be persisted in store. By default ActiveMQ have producerFlowControl set to true, and broker will notify producer to wait untill there will be place in memory, eg. messages from queue will be consumed. Second entry turn on advisory messages for message delivery and consumption for MOM.Client.\* queues. These advisory messages are sent when message was received from producer and was succesfully processed by consumer. ActiveMQ offers number of [advisory topics and messages](http://activemq.apache.org/advisory-message.html). Some of them are disabled, some other are enabled by default.

Second change in configuration file is predefined destinations.
```xml
 <destinations>
 <queue physicalName="MOM.Incoming" />
 <queue physicalName="MOM.Audit" />
 </destinations>
```

These destinations will be created when broker starts, even if there is no consumers or producers. After these changes we can start our broker with following command:
```bash
$ACTIVEMQ_HOME/bin/mom console
```
With VisualVM we can check which destinations are created by default. When you start monitoring tool simply select local process with org.apache.activemq.console.Main which should be displayer after execution of command listed above.
[![](/wp-content/uploads/2011/06/mom_jmx.png)](/wp-content/uploads/2011/06/mom_jmx.png)

That's all for ActiveMQ set up. If you have troubes with configuration changes, you can pick up all changes directly from [git repository](https://github.com/splatch/mom-sample) or download [zip archive](https://github.com/splatch/mom-sample/zipball/master) \| [tar archive](https://github.com/splatch/mom-sample/tarball/master) if you don't have git client.

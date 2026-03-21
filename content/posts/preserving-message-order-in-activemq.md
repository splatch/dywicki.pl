---
author: splatch
category:
  - middleware
cover:
  alt: activemq-logo
  image: /wp-content/uploads/2011/11/activemq-logo.png
date: "2011-11-18T10:36:52+00:00"
guid: http://dywicki.pl/?p=699
tag:
  - activemq
  - jms
title: Preserving message order in ActiveMQ
url: /2011/11/preserving-message-order/

---
Few hours ago I've found an usefull [post](http://marcelojabali.blogspot.com/2011/11/preserving-message-order-with-apache.html) about preserving message order with [ActiveMQ](http://activemq.apache.org) written by [Marcelo Jabali](http://marcelojabali.blogspot.com/) from [FUSE Source](http://www.fusesource.com/).

In his example Marcelo used broker feature called [Exclusive Consumers](http://activemq.apache.org/exclusive-consumer.html). It lets send messages only to one consumer and if it fails then second consumer gets all messages. I think it is not the best idea if we have many messages to process. Why we wouldn't use few consumers with preserved message order? Well, I was sure it is not possible, but during last training I've found solution.

## Broker configuration

So how to force ActiveMQ to preserve message order? It's really simple, we just need to change dispatch policy for destination. We can do this for all queues or only for selected.
\[xml\]
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:amq="http://activemq.apache.org/schema/core">

 <broker xmlns="http://activemq.apache.org/schema/core">
 <destinationPolicy>
 <policyMap>
 <policyEntries>
 <policyEntry queue=">"><!-- Please refer 2nd part of this post -->
 <dispatchPolicy>
 <strictOrderDispatchPolicy />
 </dispatchPolicy>
 </policyEntry>
 </policyEntries>
 </policyMap>
 </destinationPolicy>
 </broker>
</beans>
\[/xml\]

After this consumers should receive messages in same order like they were sent from producer. You can find example code on github: [example-activemq-ordered](https://github.com/splatch/example-activemq-ordered). You can run all from maven:
\[bash\]
cd broker1; mvn activemq:run
cd broker2; mvn activemq:run
cd consumer; mvn camel:run
cd consumer; mvn camel:run
cd producer; mvn camel:run
\[/bash\]

## Upgrade

After posting update about this blog post to Twitter [Dejan Bosanac](http://www.nighttale.net/) send me [few](https://twitter.com/dejanb/status/137485620292493312) [updates](https://twitter.com/#!/dejanb/status/137498395358142464). He is co-author of [ActiveMQ in Action](http://www.manning.com/snyder/) so his knowledge is much more deeper than mine. :)
First of all I mixed XML syntax. **strictOrderDispatchPolicy** is handled by topics, not queues. For second destination type strict order is turned on by **strictOrderDispatch** attribute set to true for **policyEntry** element. This preserves order but, as Dejan wrote, it will broke round robin and all messages will go to only one consumer, as in previous example given by Marcelo.

Also, Marcelo published [second post](http://marcelojabali.blogspot.com/2011/11/load-balanced-ordered-message.html) about Message Groups which allows to preserve order and have multiple concurrent consumers on queue.

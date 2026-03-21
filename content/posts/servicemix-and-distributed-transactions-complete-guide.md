---
author: splatch
category:
  - middleware
date: "2011-12-11T23:43:23+00:00"
draft: "true"
guid: http://dywicki.pl/?p=698
summary: Data consistency is a key topic in software integration. You need transactions to ensure that particular operation is saved correctly or there is something wrong. Depends on how many resources you have you might need two different transactions - local or distributed.
tag:
  - jta
  - servicemix
title: ServiceMix and distributed transactions. Complete guide.
url: /

---
Data consistency is a key topic in software integration. You need transactions to ensure that particular operation is saved correctly or there is something wrong. Depends on how many resources you have you might need two different transactions - local or distributed.

Let’s start from database operations. They are introduced to you to let you execute multiple statements. All goes or none. If you would like call two different instances, or two different database types then a standard SQL transaction will not be sufficient.

## Local transaction

A local transaction let’s you to complete two dependant operations. If first will fail then second won’t occur. If second will fail then first can still be rolled back. You can receive a message from queue and then try to persist it. For this blog entry I decided to use a bit different example. I will open database transaction, read row from table, create drop statement and send JMS message. Of course there is much more topics, not covered in this entry, related to database, for example isolation levels.
Let’s review a code:

\[java\]
public void consume() throws SQLException {
 Connection connection = dataSource.getConnection();

 connection.setAutoCommit(false);

 try {
 ResultSet resultSet = connection.createStatement().executeQuery("select id, body from notifications order by insert\_time limit 1 for update");
 if (resultSet.next()) {
 final Notification notification = new Notification(resultSet.getInt("id"), resultSet.getString("body"));
 System.out.println("Found an message " + notification);
 // lets send the delete statement to the database before we'll send message
 connection.createStatement().execute("delete from notifications where id = " + notification.getId());

 jmsTemplate.send("notifications", new MessageCreator() {
 public Message createMessage(Session session) throws JMSException {
 TextMessage message = session.createTextMessage(notification.getBody());
 message.setIntProperty("id", notification.getId());
 return message;
 }
 });
 }
 connection.commit();
 System.out.println("Changes commited");
 } catch (Exception e) {
 System.out.println("Cannot complete operation " + e.getClass());
 logger.error("Unable to complete operation", e);
 connection.rollback();
 } finally {
 connection.close();
 }
 }
\[/java\]

This really simple code uses a Spring JMSTemplate to don’t write whole stuff to get session etc. Because I will send only one message I do not need any JMS transaction. Message will be sent or not. Client library should throw an exception if the broker is not available or there is any other problem.
There is also second part of code which controls the database read operation. It allows to read a command from command line and execute it. For this particular example I used only three - list, stop and receive.

\[java\]
// todo wstawić kod
\[/java\]

Before first run we need to create an database structure. I used PostgreSQL, with following table structure:
\[sql\]
create table notifications (
id serial primary key,
body varchar not null,
insert\_time timestamp default now()
);
insert into notifications ("body") values('aaa');
insert into notifications ("body") values('bbb');
insert into notifications ("body") values('ccc');
\[/sql\]

You may ask, why I implement this kind of stuff instead of use a poller or something like that. Well, I wan’t to controll this operation to show that once my broker is started consumption works fine, but when I will stop the broker then data will stay in table.

How to run it then? Let’s start a Main class from project and use commands
\[plain\]
list
1 \| 2011-12-09 19:25:36 \| aaa
2 \| 2011-12-09 19:25:37 \| bbb
3 \| 2011-12-09 19:25:38 \| ccc
receive (broker is stopped)
Found an message \[1: aaa\]
Cannot complete operation class org.springframework.jms.UncategorizedJmsException
receive (broker is started)
Found an message \[1: aaa\]
Changes commited
\[/plain\]

## Distributed transaction

The key difference between these types are how many different elements your transaction will cover. If you will have only two resources then the local transaction will normally be sufficient.

I like back to the topics I fall in the past. Over one year ago a VoIP provider company asked me to do training and cover the distributed transactions. I fall then and did not complete this topic due many reasons,

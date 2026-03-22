---
author: splatch
category:
  - middleware
date: "2011-06-13T12:54:18+00:00"
guid: http://dywicki.pl/?p=590
title: Building sample middleware – data structures
url: /2011/06/building-sample-middleware-data-structures/

---
## Data with middleware

The data structures are very important in middleware world. Because we often connect multiple systems we need to define an "domain model" for integration. The domain model means that objects we share between all systems are **well known**, **well defined**, **well understand** in multiple teams often provided by multiple vendors. Let see what does it means in practice.

#### Well known

If you talk with business guys you have to use same terms with programmers. You might be mediator but not the translator. As integration team member mediate correct shape of solution but don't became a translator between business divisions and developers (unless you are business analitic). All people should use same terms to minimalise problems with number of definitions. That's first step of multiple projects, not only integration. If you look for deeper knowledge of domain definitions check a " [Domain Driven Design](http://domaindrivendesign.org/books/evans_2003)" book written by [Eric Evans](http://domaindrivendesign.org/about#eric) and published in 2003. Ok, maybe business is not always part of integration project, but you communicate systems used by people, isn't?

#### Well defined

You have names and scopes for your classes and objects. Probably you have 90% of fields that will be defined in them. Remember to don't put too much informations from one system to domain model. Domain model is not list of entities from one system. Make sure that you don't share database identifier from one system to second. Identifier should be business, for example most of clients might be identified by personal document, most of companies can be identified by tax id, not by database sequence. For future reasons try to extract atomic values, like for databases. With atomic values you will have less text processing to extract specific data and less code to maintain.

#### Well understand

That's case for distributed teams. Usually every team have own parts of software to do and every team plays different game. Someone else produces messages, someone consumes them, this is place when you'll have small wars. Make sure that data structures you all have to share match common needs, that defined identifiers are fine for different systems too. I met multiple situations when system analytics from two companies spent lots of hours on phone defining field constraints. Don't follow this path.

Whole integration project is about to fail in 95% cases - if you will not have data structures. Ok, we know that proper structure is key for integration. Second important point in data is representation. I don't going to talk about REST at all, but rather about format of serialization. We have number of options here, just look table below
Serialization FormatProsConsJava Object Serialization

- Very easy to maintain
- Easy to use with JMS / ActiveMQ - ObjectMessages
- Built in Java

- Available only for systems written in Java
- Both sides must use same classes and versions (if we use serialization id)
- As every binary format - it is hard to process

XML (with XML Schema)

- Easy to read and write, both for computer and people (I don't belive YAML)
- Easy to use with JMS / ActiveMQ - TextMessages
- Very easy validation, with XML Schema
- Well supported in many languages (including validation with XML Schema), DOM api provides complex operations

- Requires additional libraries, some languages don't have any binding from XML to Objects and vice versa
- Synchronisation between Java and XML Schema (code generation)
- Without proper XML Schema might be too lax
- Not the best for big portions of data because overflow

JSON

- Easy to read and write, both for computer and people, even easier than XML
- Easy to use with JMS / ActiveMQ - TextMessages
- Smaller than XML, easier to parse, even without any API
- Supported in number of languages

- Lack of well supported official structure control tool (like XML Schema)
- Lack of official API for handling JSON
- Requires additional libraries, some languages don't have any binding from JSON to Objects and vice versa

[![](/wp-content/uploads/2011/06/mom_domain.png)](/wp-content/uploads/2011/06/mom_domain.png)

There is number of different wire formats not listed here - for example [Protobuf](http://code.google.com/p/protobuf/), [EDI](http://en.wikipedia.org/wiki/Electronic_Data_Interchange) which match this scenario too. I don't count them (even if I should) because first is not well supported in many languages, second is rather legacy format not well for new systems.

## Structures used in MOM

Like in most cases, also with middleware we have some data structures. Because we going to process simple cash transactions we'll have following classes:

- org.code_house.mom.domain.Transaction
- org.code_house.mom.domain.Client
- org.code_house.mom.domain.Money
- org.code_house.mom.domain.Money.Currency

### Transaction

Every transaction will have unique identifier generated by [UUID](http://download.oracle.com/javase/1,5.0/docs/api/java/util/UUID.html). Transaction will point to some Client and have assigned [Money](http://martinfowler.com/eaaCatalog/money.html) object.

### Client

In our case client is very simple POJO which contains fields ID and name. For middleware purposes we going to use only ID. Name might be usefull for user interface operations.

### Money

Because we going to transfer some money to client account from producer we have to use proper data structure for a "cash". Few years ago [Martin Fowler](http://martinfowler.com/) in his book " [Patterns of Enterprise Application Architecture](http://martinfowler.com/books.html#eaa)" defined a [Money](http://martinfowler.com/eaaCatalog/money.html) pattern which is suitable to handle money values. We have to remember that client account might be handled in different currency and we cannot send only amount information. We need a currency too.

That's all for data structures. It was more about philosophy than about code, but don't worry - in next part we going to write more. All needed classes you'll find in [git repository](https://github.com/splatch/mom-sample). You may download [zip archive](https://github.com/splatch/mom-sample/zipball/master) \| [tar archive](https://github.com/splatch/mom-sample/tarball/master) if you don't have git client.

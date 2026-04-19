---
author: splatch
category:
  - osgi
date: "2015-02-17T10:47:48+00:00"
guid: http://dywicki.pl/?p=857
tag:
  - config-admin
  - karaf
title: Apache Felix Configuration Admin with array values
url: /2015/02/apache-felix-configuration-admin-with-array-values/

---
Apache Felix Configuration Admin (CM) is widely used component which is responsible for provisioning of one of most common OSGi services. Main responsibility of it is to bring configurations stored in property files to services.

While digging into Felix CM code I have found that it is able to create scalar values of certain type ie. Long, but also more complex structures such Array or Vector. The biggest issue was that I couldn’t find any way to force it to create array from string representation. Thanks to google ( [FELIX-4431](https://issues.apache.org/jira/browse/FELIX-4431) found on 4th page of results) and debugger goodnes I finally managed to do it. Here is recipe how to proceed.

### Configuration file

Config file name which is source of properties must be named .config – otherwise array will not be created.
Property must be written as follows:
```properties
property=\["value 1", "value 2", "value x"\]
```

Internally config admin is also storing information about value type. By default created values and collections will consist elements of type String. If you wish to change type of collection following prefixes are allowed:

- T = String
- I = Integer
- L = Long
- F = Float
- D = Double
- X = Byte
- S = Short
- C = Character
- B = Boolean

Small prefix letters represents simple type. If you want to construct array of primitive ints then configuration syntax is following:
```
property=i\["1", "2", "3"\]
```

### Small note for Karaf users

By default Karaf etc/ directory uses \*.cfg suffix as fileinstall filter which means that this feature of Felix Configuration Admin will not work for you. You have two workarounds.
Edit **etc/config.properties** and navigate to first line shown in listing and replace it with second:
```
felix.fileinstall.filter = .\*\\\.cfg
felix.fileinstall.filter = .\*\\\.(cfg\|config)
```

Create new file **org.apache.felix.fileinstall-config.cfg** with following contents:
```
felix.fileinstall.dir = ${karaf.base}/config
felix.fileinstall.tmpdir = ${karaf.data}/generated-bundles
felix.fileinstall.poll = 1000
felix.fileinstall.filter = .\*\\\.(cfg\|config)
```

### Quick summary

I am using Configuration Admin service since years and I didn’t realize this feature exists and it’s supported since very long time. Hope that this will let you to go over your more complex configurations! :-)

---
author: splatch
category:
  - osgi
date: "2024-02-20T00:13:27+00:00"
guid: http://dywicki.pl/?p=1040
summary: The Felix Fileinstall and Config Admin are two essential components of Apache Karaf runtime. I wrote about issues with array/list handling already twice. In 2015 and 2022. While earlier post was about syntax, later was about troubles with parsing of stored entries. Turns out, I've found a reason.
tag:
  - config-admin
title: Felix Config Admin with Fileinstall and array values
url: /2024/02/felix-config-admin-with-fileinstall-and-array-values/

---
The Felix Fileinstall and Config Admin are two essential components of Apache Karaf runtime. I wrote about issues with array/list handling already twice. In 2015 and 2022. While earlier post was about syntax, later was about troubles with parsing of stored entries. Turns out, I've found a reason.

When it comes to handling of configuration updates from filesystem the Fileinstall is first in the queue to carry these into runtime. However recently I was again hit by its behavior. While [earlier issue](/2022/02/apache-felix-configuration-admin-and-collections/) was solved just by making configuration read only, this time I wanted to keep it open for modifications. And problem was clear - I could write using square brackets (array) syntax, but I could not use regular brackets (collection) syntax. Over the journey I've found following.

## Typed properties

Fileinstall embeds felix-utils which ship class named `TypedProperties`. This little lovely thing can distinguish typed and non-typed properties. Later ones are treated as strings. Turns out, decision is made based on regex, which does recognize `[ a, b, c]` syntax but does not accept `( a, b, c )`. Issue known for 5 years - see [FELIX-6042](https://issues.apache.org/jira/browse/FELIX-6042).

To make things worse, fileinstall relies on outdated felix-utils release till today. I was first hit by issue with fileinstall 3.6.6 back in 2020. But problem is still present in most recent release - 3.7.4. Why? Because it does rely on felix-utils 1.11.2 which miss this patch: [https://github.com/apache/felix-dev/commit/2a8b4eba2fb99bfac727818f315ea190542c92e7](https://github.com/apache/felix-dev/commit/2a8b4eba2fb99bfac727818f315ea190542c92e7).

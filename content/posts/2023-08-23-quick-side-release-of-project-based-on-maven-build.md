---
author: splatch
category:
  - uncategorized
date: "2023-08-23T12:37:34+00:00"
draft: "true"
guid: http://dywicki.pl/?p=1024
summary: Making project release using maven release plugin is rather straight. While maven release plugin is not perfect, and aggregated a lot of hate in its early days. Still it is one of most common plugins used on daily basis, despite of historical implications. Today we are going to have a look on how to conduct a "side" release of Apache PLC4X with it.
title: Quick side release of project based on maven build
url: /

---
Making project release using maven release plugin is rather straight. While maven release plugin is not perfect, and aggregated a lot of hate in its early days. Still it is one of most common plugins used on daily basis, despite of historical implications. Today we are going to have a look on how to conduct a "side" release of Apache PLC4X with it.

Maven release plugin rely on scm information which is embedded in a `pom.xml` file. The scm information itself points directly to a scm provider compatible with maven [scm-api](https://maven.apache.org/scm/index.html). It means that maven release plugin can work with any of scm mechanisms supported by scm-api (git, svn, hg, local).

Apache PLC4X is a project which is still under heavy development. It means that its releases are a bit deferred towards a point where its API represents a stable base ready for tagging. Since our experiment is mainly about cutting a release, we can use it for a bit.

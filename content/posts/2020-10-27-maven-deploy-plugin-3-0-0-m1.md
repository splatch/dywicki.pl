---
author: splatch
category:
  - software
date: "2020-10-27T22:07:47+00:00"
guid: http://dywicki.pl/?p=1000
summary: Remote repositories are one of fundamental concepts promoted by Maven. Its use is quite common. It is handled by [maven-deploy-plugin](https://maven.apache.org/plugins/maven-deploy-plugin/), so its migration is something you will probably notice sooner or later. This short post will explain changes in new version of plugin which you need to take care of when moving from 2.8 or earlier releases.
tag:
  - maven
title: Maven deploy plugin 3.0.0-M1
url: /2020/10/maven-deploy-plugin-3-0-0-m1/

---
Remote repositories are one of fundamental concepts promoted by Maven. Its use is quite common. It is handled by [maven-deploy-plugin](https://maven.apache.org/plugins/maven-deploy-plugin/), so its migration is something you will probably notice sooner or later. This short post will explain changes in new version of plugin which you need to take care of when moving from 2.8 or earlier releases.

Most of maven builds I interact with still rely on 2.5.x version of maven deploy plugin. New one brings few significant changes:

- No support for `uniqueVersion` option, as of deploy plugin 3.0 all deployed snapshots must contain timestamp information. For example deployed snapshot is:  
`org.openhab.core.reactor.bom-3.0.0-20201027.211058-7.pom` and not  
`org.openhab.core.reactor.bom-3.0.0-SNAPSHOT.pom`.
- No support for legacy repositories, coming from changes in deployment scheme.
- In result of above parameter format for below parameters changed from `id::layout::url` to `id::url`:
  - altDeploymentRepository
  - altReleaseDeploymentRepository
  - altSnapshotDeploymentRepository

Especially last change is tricky one because it is easy to miss it. If you used plugin prior 3.0 release and just updated its version you need to amend eventual CI pipelines which relied on above, otherwise authorization information will not be used.

If you experience troubles with access and get all kinds of `HTTP 401` errors then verify if your failed build contains similar lines:

```
[INFO] --- maven-deploy-plugin:3.0.0-M1:deploy (default-cli) @ org.openhab.core.reactor ---
[INFO] Using alternate deployment repository internal::default::https://repo.myorg.com/repository/
Downloading from internal::default: https://repo.myorg.com/repository/org/openhab/core/org.openhab.core.reactor/3.0.0-SNAPSHOT/maven-metadata.xml
[WARNING] Could not transfer metadata org.openhab.core:org.openhab.core.reactor:3.0.0-SNAPSHOT/maven-metadata.xml from/to internal::default (https://repo.myorg.com/repository/): Authentication failed
```

At first look everything looks fine - you have alt deployment uri, custom server with `internal` identifier. Yet whole process blows for no reason.  
Actual reason is that maven deploy plugin looks for server called `internal::default` and not `internal`. This results in no authorization header sent to remote repository which obviously answers with `401`.

One more thing to be aware - keep in mind taht there is difference between `mvn deploy` call which calls a phase and `mvn deploy:deploy` which calls a single plugin and `org.apache.maven.plugins:maven-deploy-plugin:3.0.0-M1:deploy` call. Last one requests specific version of plugin to be used. In first two cases version of maven deploy plugin used during execution of build is determined from actual project configuration. This means that your parents or maven super pom drives that for you.

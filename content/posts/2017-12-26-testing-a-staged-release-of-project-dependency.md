---
author: splatch
category:
  - middleware
date: "2017-12-26T23:56:41+00:00"
guid: http://dywicki.pl/?p=879
tag:
  - apache
  - maven
title: Testing a staged release of project dependency
url: /2017/12/testing-a-staged-release/

---
One of most important things, if not the most important in software, is release process. There are whole books written about "shipping software" and software release is one of key parts which needs to happen in order to deliver our programs to end users. In this very short post I will give you a short tip about how to do a test drive of a release which is not published yet. One of main principles of maven central is "what goes there, stays there", meaning that anything which becomes public will stay public. For that reason we, as software developers, want to deploy things which are free of any major issues at release time.
[Staged release](https://maven.apache.org/guides/development/guide-testing-releases.html) is one of things which are supported by maven-release-plugin. Overall idea behind this is to let people have a test drive before deploying artifacts to public repositories from which they can not be removed. Of course this might be seen as completely unnecessary step if you release a small library, but could be extremely useful for bigger projects, avoiding something I would call a quick fix hiccup.

### Quick fix hiccup

Situation when project get released often is welcome. But situation when project gets released with bugs is not welcome at all. Again, this might depend on actual use case, some small failures discovered during release might be accepted, some other can be a show stopper. A quick fix hiccup happens when project gets released and then people who start using it keep discovering important issues which lead to another release.
Lets assume we released a new major version called 3.0.0. With this release there is new issue discovered, so we release 3.0.1. After that we find another potentially dangerous bug generating new set of artifacts in version 3.0.2.
As you can see - in both cases bug fixes have been made, however people who already started using project released as 3.0.0 needs to bump it twice in very short time window. Wouldn't it be better to do one release but without major bugs instead?

### Staged release

Someone who been thinking about this problem come to very simple conclusion - if problem is impossibility or inconvenience caused by unpublishing of artifacts, then it might be better to hold on for a moment and let people test binaries before they get published.

Deployment phase in such release process is divided into two stages - first moving artifacts to test repository and second moving them from test repository to final one. A test repository can be anything - it can be an FTP server, maybe a filesystem. In general, it is just a place which is accessible to interested parties who would like to test our artifacts.
Once testing phase is done and no major issues are found artifacts are deployed to public repository. For regular projects that would be maven central or any other location accessible for wider publicity.

### Testing of staged release

Here we are going to a technical part of this article - which is - how to become a tester of staged artifacts. I will use maven as reference, but you can use any other build tool which is capable of downloading contents from configurable locations.

Once we know what is location of our binary artifacts which can be used for testing we need to modify maven settings.xml to include new remote repository:

```xml
<profile>
    <id>karaf-4.1.4</id>

    <activation>
        <activeByDefault>false</activeByDefault>
    </activation>

    <repositories>
        <repository>
            <id>karaf-4.1.4</id>
            <name>Karaf 4.1.4 Stage Repo</name>
            <url>https://repository.apache.org/content/repositories/orgapachekaraf-1102/</url>
            <snapshots><enabled>false</enabled></snapshots>
            <releases><enabled>true</enabled></releases>
        </repository>
    </repositories>
</profile>
```

This is additional profile which can be quickly removed after release is finished or when testing is done. By this short piece of code you will force your maven installation to scan additional remote location for metadata and released artifacts. With such profile enabled you can build your software and verify that dependency which is about to get released works in your project.

### How to build with staged dependency and rollback

It is important to be aware what is difference between maven `install` and package phases. Here, just as recap I will mark that in most of cases we should use `package` phase because `install` changes state of local repository. Profile defined above will local repository too by downloading things not available in maven central. In case when release will be cancelled binaries which we downloaded from staging repository will be invalid and their checksum will differ. More importantly your local copy of dependency will be outdated and will not contain any fixes deployed after.
For that reason usage of staged artifacts should be combined with temporary local repository to avoid above troubles in future. You can point alternate maven repository from command line via `maven.repo.local` property. You can also modify it in your maven settings, however it is less convenient. You can also create a temporary settings.xml which will be used only for testing and point it via `--settings` or `-s` option from command line.

### Final thoughts

We all know there is no software which is free of bugs. Staged releases does not guarantee software without bugs, but they help a lot when software runs in many various environments and authors can not test nor identify all possible use cases.
Software quality depends on many factors. Quality assurance is very important, but all efforts put there are made to make sure that software works as expected for end users. Letting them run software, after all internal checks are made but before it gets published and announced ensures that software is free of any major bugs, at least the part of community which did test it.

A small note at the end on "administrative" overhead caused by staged releases. Article on maven.apache.org describes manual procedure necessary to setup [Apache Archiva](http://archiva.apache.org). This area has been [improved in Archiva 1.4](http://archiva.apache.org/docs/1.4-M4/adminguide/staging-repositories.html).

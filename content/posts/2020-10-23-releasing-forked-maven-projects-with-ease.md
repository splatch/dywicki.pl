---
author: splatch
category:
  - software
date: "2020-10-23T23:54:11+00:00"
guid: http://dywicki.pl/?p=989
summary: In this pretty short introduction I am going to describe you a trick which I learned recently while doing new CI/CD configuration for one of open source projects I work with.
tag:
  - gitlab
  - maven
title: Releasing forked Maven projects with ease
url: /2020/10/releasing-forked-maven-projects-with-ease/

---
In this pretty short introduction I am going to describe you a trick which I learned recently while doing new CI/CD configuration for one of open source projects I work with.

Whole problem in forks is cost of their creation and maintenance. Obviously we do our own releases for various reasons. Starting point for what I am writing here is another remote GIT repository which contains several extra commits awaiting, let say for official release from origin maintainers.

```bash
$ git remote -v
github	git@github.com:splatch/openhab-core.git (fetch)
github	git@github.com:splatch/openhab-core.git (push)
origin	git@github.com:openhab/openhab-core.git (fetch)
origin	git@github.com:openhab/openhab-core.git (push)

```

You can't hold your company product release due to 3rd party which takes too long to process your PR, so you fork and release internally. What you probably don't want to do is handcrafting of Maven POMs just to get release done to your internal repository.

Interesting part is **how to perform fork-release without any pom updates**. Some projects, which have mixed commercial/open source use, allow to configure distribution management (snapshots & releases) through properties, some do not.  
In reality most of projects do not bother about that, being busy with bugs reported by users. Almost certainly it will not allow to specify project repository URL where tag should be created.

Each Maven project which gets released through all sorts of plugins have SCM information. Something like this:

```xml
  <scm>
    <connection>scm:git:https://github.com/openhab/openhab-core.git</connection>
    <developerConnection>scm:git:https://github.com/openhab/openhab-core.git</developerConnection>
    <tag>HEAD</tag>
    <url>https://github.com/openhab/openhab-core</url>
  </scm>

  <distributionManagement>
    <repository>
      <id>bintray</id>
      <url>${oh.repo.distBaseUrl}/openhab-core/;publish=1</url>
    </repository>
    <snapshotRepository>
      <id>jfrog</id>
      <url>${oh.repo.snapshotBaseUrl}/libs-snapshot-local</url>
    </snapshotRepository>
  </distributionManagement>
```

When you do internal release you usually change above sections to match your fork URI and repository manager.

It turns out that it is possible to avoid POM updates and rely on basic operations which will let maven use new locations. All you need are custom maven `settings.xml` file plus few switches and one trick.

## Use fork instead of origin

You need to tag your fork, not an origin to which you have no access. To do such thing we can utilize `resume` option for [prepare](http://maven.apache.org/maven-release/maven-release-plugin/prepare-mojo.html) goal from [maven-release-plugin](http://maven.apache.org/maven-release/maven-release-plugin/index.html). Resume lets you continue release process if it breaks. Whole thing rely on existence of `release.properties` file from prior attempt. This means that we can prepare contents of this file for our release and just continue.  
If you will have a look into this file there are couple of entries related to scm repository, but most important is `scm.url`. If you set it to valid [Maven SCM URI](http://maven.apache.org/scm/scm-url-format.html) then scm set in project is ignored. :-)

So our release script should start from:

```
echo "scm.url=scm:git:$CI_REPOSITORY_URL" >> release.properties
```

## Use own maven repository

Second step is really preparation of options for `release:perform` call. I skip preparation, cause it's rather straight. The trick is how to override distribution management.

We know that deploy is done by [deploy:deploy](https://maven.apache.org/plugins/maven-deploy-plugin/deploy-mojo.html) goal of [maven-deploy-plugin](https://maven.apache.org/plugins/maven-deploy-plugin/). Starting from version 3.x line (btw 3.0.0.M1 is over two years old at moment of writing) it does support `altReleaseDeploymentRepository` option.

Knowing this our perform call should look like this:

```
mvn release:perform -Darguments="-DaltReleaseDeploymentRepository=$SERVER_ID::default::$SERVER_URL"
```

As far I remember the format of option is slightly different from `altDeploymentRepository`, so you need to test it.  
With these two things your jenkins should be able to pick pretty much any repo and tag it at any point of time with no issues.

## Final script

Given that above snippets are not complete here you will find all-in-one. I skip `.mvn/ci-settings.xml` as it needs to define necessary `server` sections. I also omit `.mvn/release.config` file which should contain list of extra parameters for `release:prepare` and `perform` calls.

```bash
#!/bin/bash

if [ ! -f .mvn/ci-settings.xml ];
  then echo "The .mvn/ci-settings.xml file is missing.";
  exit 1
fi

if [[ -z "${CI_REPOSITORY_URL}" ]];
then
  echo "You must set CI_REPOSITORY_URL pointing to tag repository location, ie. git@github.com:foo/bar.git"
  exit 1
fi

if [[ -z "${RELEASE_VERSION}" || -z "${DEVELOPMENT_VERSION}" ]];
then
  echo "You must set RELEASE_VERSION pointing DEVELOPMENT_VERSION variables before calling script"
  exit 1
fi

releaseOptions=$(<.mvn/release.config)
releaseOptions=$(echo $releaseOptions|sed "s/\n/ /g")

echo "scm.url=scm:git:$CI_REPOSITORY_URL" >> release.properties
mvn -s .mvn/ci-settings.xml $MAVEN_CLI_OPTS $MAVEN_OPTS_EXTRA $releaseOptions release:prepare \
  -DautoVersionSubmodules=true \
  -DreleaseVersion=${RELEASE_VERSION} \
  -DdevelopmentVersion=${DEVELOPMENT_VERSION} \
  -Dtag=openhab-${RELEASE_VERSION} \
  -Darguments="$releaseOptions" \
  -DpreparationGoals="clean install"

preparationStatus=$?

if [[ $preparationStatus -eq 0 ]];
then
  echo "========================================"
  echo "Performing release of ${RELEASE_VERSION}"
  echo "========================================"

  mvn -s .mvn/ci-settings.xml $MAVEN_CLI_OPTS $MAVEN_OPTS_EXTRA release:perform \
    -Darguments="-s $PWD/.mvn/ci-settings.xml ${releaseOptions}" \
    -Dgoals="deploy"
fi
# call it with
RELEASE_VERSION=x DEVELOPMENT_VERSION=y CI_REPOSITORY_URL=z ./ci-release.sh
```

Enjoy yor forks! :-)

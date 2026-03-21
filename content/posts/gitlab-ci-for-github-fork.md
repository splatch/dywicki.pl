---
author: splatch
category:
  - rants
  - software
date: "2020-10-26T21:09:27+00:00"
guid: http://dywicki.pl/?p=994
summary: One of most annoying things these days is managing CI/CD with multiple repositories. For quite long time github didn't offer private repositories while gitlab did. This lead to situation where people been asking gitlab for additional features. One of these is support for mirroring git repositories from github to gitlab just to run pipelines. I happened to have the same requirement.
tag:
  - gitlab
  - maven
title: Gitlab CI for github fork
url: /2020/10/gitlab-ci-for-github-fork/

---
One of most annoying things these days is managing CI/CD with multiple repositories. For quite long time github didn't offer private repositories while gitlab did. This lead to situation where people been asking gitlab for additional features. One of these is support for mirroring git repositories from github to gitlab just to run pipelines. I happened to have the same requirement.

I've run into github-gitlab scenario already several times and never really found a good-enough solution. We are lucky that github eventually started to fight for CI/CD market. Their `workflows` makes it possible to run `actions` of various kinds. It seems to be an awesome tool, yet I found it confusing. Learning another syntax after spending several years with competitors (circle, travis, gitlab) seems to be.. redundant. Without time to learn new tool I had to adopt existing ones.

One of things I realized is that Gitlab started to support scheduled pipelines. Making a cron job is doable, just like with good old Jenkins. More over, it seems to be possible to detect if job execution is triggered by push or schedule event.

My desired setup is like this:

```
[github/openhab]       [     gitlab   ]       [github/fork   ]
[ openhab-core ] ----> [ openhab-core ] ----> [ openhab-core ]
                              ^
                              |
                              `- CI/schedule is here
                                 As well as additional jobs
```

Because openHAB project uses Travis CI and Jenkins adding gitlab pipeline didn't make much sense for them. Obviously it is a common scenario that you must add `.gitlab-ci.yml` somewhere. Remember, we talk about free plans and limited options. We do not have access to source repository to stick with repository triggers. The only option is polling for new commits.

Equipped in this knowledge (after several hours of fruitless attempts) I made my first scripted attempt which adds new remote, fetch changes from it, then push update to gitlab and github forks. An important note here, script uses rebase - so it tries to put my commits (in this case its only CI config) on top of remote ones.

```bash
#!/bin/bash
# dear executor, please help me debugging this...
# set -xv
# env

# Below script uses two tokens - one to push changes to github (GITHUB_MIRROR_TOKEN) and other to push back rebased changes to gitlab (GITLAB_TOKEN).

# Git complains on rebase as it must have some indication of author
git config user.email "bot@myorg.com"
git config user.name "MyOrg Bot"

# get repository path without username/password and scheme
repoUrl=$(git remote get-url origin | cut -d '@' -f 2)
git fetch origin
git remote set-url origin "https://gitlab-ci-token:${GITLAB_TOKEN}@${repoUrl}"
git remote add --tags openhab https://github.com/openhab/openhab-core.git
git remote add connectorio --tags https://${GITHUB_MIRROR_TOKEN}:x-oauth-basic@github.com/ConnectorIO/openhab-core.git
git fetch --unshallow openhab
git pull --rebase openhab master
git push --tags -f origin HEAD:master
# push all but last commit to github
git push --tags -f connectorio HEAD~1:master
```

This is already working version with all adjustments cause `--unshallow` was needed to push openhab tags to fork and gitlab mirror.

Whole procedure is:

- Setup git author information
- Prepare "push" url
- Fetch changes from gitlab
- Override gitlab remote url, so writes are permitted
- Add source repository (openhab)
- Add github fork (connectorio)
- Fetch changes from source repo and rebase CI/CD config on top of it
- Push updated code to gitlab
- Push updated code (without CI/CD config) to github fork

Pipeline I tested was quite simple:

```yaml
stages:
  - mirror
  - build
  - deploy

mirror:
  stage: mirror
  script: ./.gitlab/sync.sh
# build & deploy sections
```

## Duplicate job executions

Shortly after I committed first revision I realized that such script can run forever just doing empty pulls and empty pushes. A naive approach I took would cause second build with same changes as mirror sync. I had to look for exit or entry condition which could drive execution across different paths. First one is - synchronization of repositories, second one is - maven build and deploy. I didn't need to run both at the same time. For many reasons sync could be left alone to signal only synchronization issues and not build failures. This means that `git pull --rebase openhab master` is primary command to watch. Obviously above script doesn't do any checks - you can build these yourself as my main goal here is cyclic deployment of fresh snapshots for further integration builds.

Once I started to dig into `.gitlab-ci.yml` syntax I found a mysterious element called `rules`. Normally I used `only/refs` and `except/refs`, but this one really matched my case. Rules support basic conditions such `if`, `exists` and `changes`. It turns out that `only` can be paired with `variables` giving similar result to branch filtering, so if any of my advice here doesn't fly for any reason, try it. My complete pipeline definition is below:

```yaml
stages:
  - mirror
  - build
  - deploy

mirror:
  stage: mirror
  script: ./.gitlab/sync.sh
  rules:
    - if: '$CI_PIPELINE_SOURCE == "schedule"'

build:
  stage: build
  script:
    - 'mvn -s .gitlab/settings.xml $MAVEN_CLI_OPTS $MAVEN_OPTS_EXTRA package -DnoOhSnapRepo'
  rules:
    - if: '$CI_PIPELINE_SOURCE != "schedule"'

deploy:
  stage: deploy
  script:
    - if [ ! -f .gitlab/settings.xml ];
      then echo "The .gitlab/settings.xml file is missing.";
      fi
    - mvn $MAVEN_CLI_OPTS $MAVEN_OPTS_EXTRA -s .gitlab/settings.xml
      package org.apache.maven.plugins:maven-deploy-plugin:3.0.0-M1:deploy   -DaltSnapshotDeploymentRepository="${MAVEN_SERVER_ID}::${SNAPSHOT_REPOSITORY_URL}"
      -Dmaven.test.skip.exec=true -DskipChecks -DnoOhSnapRepo
  rules:
    - if: '$CI_PIPELINE_SOURCE != "schedule"'
```

## Conclusions & CI/CD rants

Nowadays CI/CD landscape is different than what we had a decade ago. Some things are simpler, some are more complex than before. I still remember Hudson/Jenkins split in 2000's, awful UI, never ending Jenkins build queue, fun with management of slave nodes and inventions around `Jenkinsfile` and job dsl. Sometimes I even miss these, because with Jenkins you could control everything. Reality is - no small company can sacrifice a man to continuously maintain CI/CD installation. Present difficulty is - how to achieve as much as Jenkins with smaller resource commitment?

The resource commitment is a critical point. I did and got paid for many strange things in past - ie. as part of one task I was automating release process (as I become a client release monkey). First releases were nightmare due to complex nature of pipeline. Once I even ended visiting Jenkins slave workspace through SSH to finish hours long product release. Whole thing blew up at rpm build during perform stage which did not trigger in my Maven build on mac. Beauty of situation was - I was able to fix situation in ad-hoc manner (read whole evening and half of the night), just by tweaking rpm spec.

All above changed with containerization which made it simpler to run an abstract executor locally in sandbox manner. We can have execution, but state is volatile. It is kept in other tools which simply store build artifacts. On one hand it is much easier to reproduce build, on other it is much harder to debug it when something goes wrong. Anyone who ever tried to find why gitlab executor does something odd running in Kubernetes knows what I mean.

In the end, lets enjoy our forks! :-)

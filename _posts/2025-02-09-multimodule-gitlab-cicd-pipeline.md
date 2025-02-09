---
layout: post
title: Multimodule GitLab CICD Pipeline: Parent Child Pipeline
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Gitlab, Multi Module Project, Ci Cd Pipeline, Deployment Pipelines, Maven]
author: keylearn
---

In this story illustrates how to trigger the pipeline for a module in multi-module project when there are any changes to the particular module. As well as trigger the pipeline for parent when there are changes to the parent project.

As per the [GitLab documentation](https://docs.gitlab.com/ee/ci/pipelines/pipeline_architectures.html), there are mainly three pipeline structures.

1. Basic: Good for straightforward projects where all the configuration is in one easy to find place.

2. Directed Acyclic Graph: Good for large, complex projects that need efficient execution.

3. Parent-child pipelines: Good for monorepos and projects with lots of independently defined components.

Let’s assume that you are maintaining maven multi-module project which has number of micro-services as modules and those are independent. If you choose basic structure to build pipeline, it might take significant time to complete the pipeline. Also, unnecessarily you are building the whole project sometimes some modules don’t have any changes even. In this type of scenarios definitely you can think of parent-child pipeline structure to create the pipeline in GitLab. You can execute the pipeline for a module independently if there are any changes to the particular module.

Let’s start to set-up Parent-child pipeline in GitLab.

First you need to create maven multi-module project with module-a and module-b.

Then you need to create a `.gitlab-ci.yml` file in parent project like mentioned in below.

~~~
#parent gitlab-ci
 
stages:
  - triggers
  - parent-clean
  - triggers-all
 
# module-a pipeline will execute if any file change in module-a project
trigger_a:
  stage: triggers
  trigger:
    include: module-a/.gitlab-ci.yml
  only:
    changes:
    - "module-a/**/*"
 
# module-b pipeline will execute if any file change in module-b project
trigger_b:
  stage: triggers
  trigger:
    include: module-b/.gitlab-ci.yml
  only:
    changes:
    - "module-b/**/*"
 
# parent pipeline will execute if any file change in parent project
trigger_parent:
  image: maven:latest
  stage: parent-clean
  script:
    mvn clean package
  only:
    changes:
    - "**"
~~~

Now, let’s go to module-a and create another `.gitlab-ci.yml` file. Then paste the content below.

~~~
#child module-a gitlab-ci
 
stages:
  - clean
  - package
 
image: maven:latest
 
build-java:
  stage: clean
  script:
    - echo "MODULE A Clean"
    - cd module-a/
    - mvn clean
 
build-package:
  stage: package
  script:
    - echo "MODULE A Package"
    - cd module-a/
    - mvn package
~~~

Now update the `.gitlab-ci.yml` with above content with relevant changes in module-b.

That’s it!. Now you can change the each module and see how pipeline work.
Thank you for following the story, happy learning.

Repository Link — https://github.com/lahirumw/gitlab-cicd-multimodule
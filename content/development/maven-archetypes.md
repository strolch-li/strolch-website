---
title: 'Maven Archetypes'
weight: 40
---

## Maven Archetypes

Maven offers archetypes to generate new projects. Strolch offers the following archetypes, to create new projects:
* [li.strolch.mvn.archetype.main](/development/main-class-app) for Java main class applications
* [li.strolch.mvn.archetype.webapp](/development/web-app) for Java Web based applications using REST and Polymer 1.x as the frontend.

To use the archetypes, clone the archetypes repository and install it locally:

```shell
git clone git@github.com:strolch-li/strolch-maven-archetypes.git
cd strolch-maven-archetypes
mvn clean install
```

Then follow one of the next steps to create the type of application you want.
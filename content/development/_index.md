---
title: 'Development'
weight: 90
---

## Prerequisites
To start developing Strolch you need an installed:
* Java JDK 11
* Apache Maven 3.x

## Building Strolch

Setting up Strolch is just a few lines:

```shell
git clone https://github.com/strolch-li/strolch.git
cd strolch
mvn clean install -DskipTests
```

{{% notice tip %}}
Note: To run the tests you will need to configure the PostgreSQL Databases. See
the README in the module.
{{% /notice %}}

After running the Maven build, you will have a full build of all Strolch
projects. Now you can start modifying the projects, and add your own features,
or, far more interesting, start developing your projects using the Strolch
agent.

## Creating a Strolch App

To create your own Strolch App, you can use Maven's archetype generation. There
are two versions, one is a simple Java App which you can use to directly access
the Strolch runtime, and the second is to create a Java Web App, which is the
usual way to run Strolch runtimes.

{{% notice tip %}}
Note: you need to have installed Strolch to your local maven repo, otherwise the
archetype won't be available. 
{{% /notice %}}

### Creating a Java Strolch Web App

The following shows the maven command to create the new maven project. Note that you should replace the placeholders in the brackets:

```shell
mvn archetype:generate \
  -DarchetypeGroupId=li.strolch \
  -DarchetypeArtifactId=li.strolch.mvn.archetype.webapp \
  -DarchetypeVersion=1.6.0-SNAPSHOT \
  -DgroupId=<my.groupid> \
  -DartifactId=<my-artifactId> \
  -Dversion=<my.version> \
  -DappName="<my app name>"
```

#### Install the web dependencies

The Strolch Web App uses [NodeJS v11.x](https://nodejs.org/download/release/v11.15.0/) to build the web dependencies. Please
download the relevant platform's package, unpack it, and add the `bin` directory
to your path variable.

Once NodeJS is installed, then you can prepare the web dependencies:

```shell
cd src/main/webapp/
npm install gulp -g
npm install
gulp
```

{{% notice tip %}} Note: Whenever the bower.json is changed then you should
again call npm install inside the webapp folder. {{% /notice %}}

#### Building the WAR

Building the WAR uses the `package` maven goal, but to have the optimized WAR
use the `release` profile:

```shell
mvn clean package -Prelease
```

Happy coding =))

### Creating a simple Java Strolch App

The following shows the maven command to create the new maven project. Note that
you should replace the placeholders in the brackets:

```shell
mvn archetype:generate \
  -DarchetypeGroupId=li.strolch \
  -DarchetypeArtifactId=li.strolch.mvn.archetype.main \
  -DarchetypeVersion=1.6.0-SNAPSHOT \
  -DgroupId=<my.groupid> \
  -DartifactId=<my-artifactId> \
  -Dversion=<my.version> \
  -DappName="<my app name>"
```

You change into the directory of the new project and then build the project by calling:

```shell
cd <my-artifactId>
mvn clean package

```

Start the program using:

```shell
mvn exec:java
```

Happy coding =))

## Tools used

The following tools are used to develop Strolch and Strolch-based projects: 

* [IntelliJ](https://www.jetbrains.com/idea/download/#section=linux "{target='_blank'}")
* [Apache Maven](https://maven.apache.org/ "{target='_blank'}")
* [Git SCM](http://git-scm.com/ "{target='_blank'}")



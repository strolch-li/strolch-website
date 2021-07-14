---
title: 'Development'
weight: 90
---

## Prerequisites
To start developing Strolch you need an installed:
* Java JDK 11
* Apache Maven 3.x

## Building Strolch

{{% notice tip %}} Note: You don't have to build Strolch if you want to use the
version on Maven central, but if you need a snapshot version, or the release you
want isn't on central, then go ahead and build Strolch. {{% /notice %}}

Building Strolch is just a few lines:

```shell
git clone https://github.com/strolch-li/strolch.git
cd strolch
mvn clean install -DskipTests
```

{{% notice warning %}}
Note: To run the tests you will need to configure the PostgreSQL Databases. See
the README in the module.
{{% /notice %}}

After running the Maven build, you will have a full build of all Strolch
projects. Now you can start modifying the projects, and add your own features,
or, far more interesting, start developing your projects using the Strolch
agent.

## Converting an existing application

You can convert an existing application to a Strolch agent, but this might be a
bit daunting in the beginning. If you are planning on doing this, first create a
test application using the maven archetypes, so that you can get a feel for the configuration.

Once that works, use the archetypes configuration to reconfigure your project to start as a Strolch agent.

{{% notice warning %}}
Note: Beware to select the archetype pertaining to your use case:
  * For a web app use the [li.strolch.mvn.archetype.webapp](/development/#creating-a-java-strolch-web-app)
  * For a application with a main method use [li.strolch.mvn.archetype.main](/development/#creating-a-simple-java-strolch-app)
{{% /notice %}}

## Creating a Strolch App

To create your own Strolch App, you can use Maven's archetype generation. There
are two versions, one is a simple Java App which you can use to directly access
the Strolch runtime, and the second is to create a Java Web App, which is the
usual way to run Strolch runtimes.

{{% notice warning %}}
Note: you need to have installed Strolch to your local maven repo, otherwise the
archetype won't be available. 
{{% /notice %}}

### Creating a Java Strolch Web App

The following shows the maven command to create the new maven project. Note that you should replace the placeholders in the brackets:

{{% notice tip %}}
The code is also available on [GitHub](https://github.com/strolch-li/strolch/tree/develop/li.strolch.mvn.archetype.webapp).
{{% /notice %}}

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

{{% notice warning %}} Note: Whenever the bower.json is changed then you should
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

{{% notice tip %}}
The code is also available on [GitHub](https://github.com/strolch-li/strolch/tree/develop/li.strolch.mvn.archetype.main).
{{% /notice %}}

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

* [IntelliJ](https://www.jetbrains.com/idea/download/#section=linux)
* [Apache Maven](https://maven.apache.org/)
* [Git SCM](http://git-scm.com/)

